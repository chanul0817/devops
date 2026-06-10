## 쿠버네티스의 Probe란?

Pod 내부 앱이 정상 동작할 때만 **트래픽을 받도록** 도와주는 기능

### 사용 이유
- Pod는 살아있어도 앱은 죽어있을 수 있음 → 이를 구분하고 대응
- Service는 Pod로 트래픽을 라우팅함 → 앱 상태 체크 필요

---

## Probe 종류

### 1. Startup Probe
- **앱 기동 확인용**
- Spring 초기화, DB 연결 등 시간이 걸리는 작업 동안 기다림
- 이 probe가 **성공해야만** 다른 probe들이 동작 시작

### 2. Liveness Probe
- **앱이 살아 있는지 확인**
- 실패가 반복되면 컨테이너 **자동 재시작**

### 3. Readiness Probe
- **트래픽 받을 준비가 되었는지 확인**
- 실패 시, 해당 Pod로는 **트래픽이 가지 않음**

---

## ReadinessProbe 사용 예시

1. **초기 로딩 처리**
   - DB 데이터 검증/초기화 등 작업 중에는 트래픽 차단 가능
   - Liveness는 성공하더라도, Readiness는 대기 가능

2. **부하 조절**
   - 앱에 부하 생겼을 때 빠르게 Readiness 실패 처리
   - 트래픽 차단 후 로직 처리 시간 확보

---

## Probe 설정 예시

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-server
spec:
  template:
    spec:
      containers:
        - name: api-server
          image: api-server:1.0.0
          ports:
            - containerPort: 8080
          startupProbe:
            httpGet:
              path: /actuator/health
              port: 8080
            failureThreshold: 30
            periodSeconds: 5
          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8080
            initialDelaySeconds: 5
            periodSeconds: 10
            failureThreshold: 3
          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8080
            initialDelaySeconds: 30
            periodSeconds: 10
            failureThreshold: 3
```

- `startupProbe`는 앱이 완전히 뜰 때까지 기다리는 용도
- `readinessProbe`는 Service 트래픽을 받을지 판단하는 용도
- `livenessProbe`는 앱이 복구 불가능한 상태인지 판단하고 재시작하는 용도

### 다른 체크 방식 예시

1. **TCP 체크**
   - 포트가 열렸는지만 빠르게 확인할 때 사용

```yaml
readinessProbe:
  tcpSocket:
    port: 3306
  periodSeconds: 5
```

2. **exec 체크**
   - HTTP 엔드포인트가 없고, 파일 존재 여부나 프로세스 상태를 직접 보고 싶을 때 사용

```yaml
livenessProbe:
  exec:
    command:
      - cat
      - /tmp/healthy
  periodSeconds: 10
```

### 실무에서 많이 보는 구성

Spring Boot처럼 기동이 느린 앱은 보통 이렇게 나눠서 생각함

- `startupProbe`: 애플리케이션이 완전히 뜰 때까지 기다림
- `readinessProbe`: DB 커넥션 풀 초기화나 캐시 워밍업 끝났는지 확인
- `livenessProbe`: 스레드가 멈췄거나 응답이 안 오는 상태만 확인

예를 들어 배포 직후 `/actuator/health`는 200인데 실제로는 마이그레이션이 안 끝난 상태일 수 있음  
이럴 때 readiness를 별도로 두면 Pod는 떠 있어도 Service 뒤로는 바로 안 붙어서 덜 위험함

```yaml
startupProbe:
  httpGet:
    path: /actuator/health
    port: 8080
  periodSeconds: 5
  failureThreshold: 24
readinessProbe:
  httpGet:
    path: /actuator/health/readiness
    port: 8080
  periodSeconds: 5
  failureThreshold: 3
livenessProbe:
  httpGet:
    path: /actuator/health/liveness
    port: 8080
  periodSeconds: 10
  failureThreshold: 3
```

### 헬스 체크 엔드포인트를 왜 나누냐

- `/actuator/health` 하나로 다 처리하면 편해 보이는데, 실무에서는 readiness랑 liveness가 원하는 기준이 다를 때가 많음
- 예를 들어 DB가 잠깐 느려져도 앱 프로세스 자체는 살아 있으면 liveness는 통과시키고, readiness만 실패시켜서 트래픽만 잠깐 빼는 쪽이 덜 위험함
- 반대로 liveness까지 같은 체크를 쓰면 DB 한 번 흔들릴 때 Pod가 우르르 재시작돼서 더 복잡해질 수 있음
- 그래서 readiness는 외부 의존성까지 조금 더 넓게 보고, liveness는 진짜 재시작이 필요한 상황만 보게 나누는 편이 보통 안전했음

---

## 운영 시 설정 기준

| 옵션 | 의미 | 설정 기준 |
| --- | --- | --- |
| `initialDelaySeconds` | 첫 체크 전 대기 시간 | 앱 시작 시간이 일정할 때 사용 |
| `periodSeconds` | 체크 주기 | 너무 짧으면 불필요한 부하 발생 |
| `timeoutSeconds` | 응답 대기 시간 | 네트워크 지연을 고려해서 설정 |
| `failureThreshold` | 실패 허용 횟수 | 일시적인 지연으로 재시작되지 않도록 조절 |

### 자주 하는 실수

- Liveness Probe에 DB 연결 확인처럼 외부 의존성이 큰 체크를 넣으면, DB 장애 때 Pod가 계속 재시작될 수 있음
- Readiness Probe 없이 배포하면, 앱이 아직 준비되지 않았는데 Service가 트래픽을 보낼 수 있음
- Startup Probe 없이 느리게 뜨는 앱에 Liveness Probe만 설정하면, 기동 중인 컨테이너가 계속 재시작될 수 있음

### 운영에서 한번씩 겪는 상황

- 배포 직후 `CrashLoopBackOff`가 반복되면, 앱 자체 오류인지 Probe 타이밍 문제인지 같이 봐야 함
- `kubectl describe pod <pod명>`으로 이벤트를 보면 `Liveness probe failed`, `Readiness probe failed`가 바로 보여서 원인 찾기 쉬움
- readiness 실패가 길어지면 Pod는 살아 있어도 Service 뒤에서는 빠지기 때문에, ALB Ingress나 API 응답 수가 갑자기 줄어든 것처럼 보일 수 있음
- `kubectl get pod`에서는 Running인데 요청이 계속 503이면 readiness 실패부터 보는 게 빠름
- 앱 로그에는 에러가 없는데 Pod만 재시작되면, `/actuator/health` 응답 시간과 `timeoutSeconds`가 너무 타이트한 경우도 많음
- Spring 앱이 느리게 뜨는데 startupProbe 없이 liveness만 먼저 걸어두면, 로그상으로는 기동 중인데도 계속 죽었다 살아나는 경우가 꽤 자주 나옴
- 이런 경우에는 `kubectl logs --previous <pod명>`으로 직전 컨테이너 로그까지 같이 보는 게 도움이 됨

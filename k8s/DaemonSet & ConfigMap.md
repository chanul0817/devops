# Kubernetes DaemonSet & ConfigMap 정리

---

## DaemonSet

### 정의
DaemonSet은 **모든(또는 일부) 노드에 Pod를 1개씩 배포**하는 리소스입니다.

### 사용 목적
- 로그 수집 에이전트 (예: Fluentd)
- 모니터링 에이전트 (예: Prometheus Node Exporter)
- 시스템 보안 도구 (예: Falco)
- 스토리지 플러그인 등 노드별 실행이 필요한 경우

### 주요 특징
- 노드가 추가되면 자동으로 해당 노드에도 Pod 생성
- 노드가 제거되면 관련 Pod도 삭제됨
- `nodeSelector`, `tolerations` 등을 통해 특정 노드에만 배포 가능

### 예시
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-agent
spec:
  selector:
    matchLabels:
      app: log-agent
  template:
    metadata:
      labels:
        app: log-agent
    spec:
      containers:
        - name: log-agent
          image: my-log-agent:latest
          volumeMounts:
            - name: varlog
              mountPath: /var/log
      volumes:
        - name: varlog
          hostPath:
            path: /var/log
```

---

## ConfigMap

### 정의
ConfigMap은 **환경 설정값(key-value)** 을 저장하고 **Pod가 외부 설정을 주입받도록** 도와주는 리소스입니다.

### 사용 목적
- 애플리케이션의 설정 분리 (코드와 설정 분리)
- 환경 변수, 커맨드라인 args, 설정파일로 주입 가능

### 주입 방법
- 환경 변수로 주입
- volume으로 마운트
- command/args로 사용

### 예시
#### 1. ConfigMap 생성
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: "production"
  LOG_LEVEL: "debug"
  config.json: |
    {
      "port": 8080,
      "debug": true
    }
```

#### 2. Pod에서 사용
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  containers:
    - name: myapp
      image: myapp:latest
      env:
        - name: APP_ENV
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_ENV
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: app-config
```

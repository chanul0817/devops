# Pod 종료에 대한 정리


## Pod가 종료되는 주요 상황

1. **직접 삭제 명령 실행**
   - `kubectl delete pod xxx` 명령 등

2. **Replica 수 변경**
   - Deployment나 StatefulSet의 replica 수를 줄일 경우

3. **스케일링 정책에 의한 자동 감소**
   - Horizontal Pod Autoscaler 등

4. **자원 정책에 의한 종료**
   - Pod QoS, ResourceQuota, PriorityClass 등에 의해 제거될 수 있음

5. **노드 장애 또는 관리 작업**
   - 노드에 장애 발생
   - `kubectl drain` 명령 실행 시

6. **작업 완료 후 자동 종료**
   - 예: Job 타입의 Pod는 작업 완료 후 종료되도록 설정 가능

---

## 종료 원인 확인 방법

- Pod가 삭제되면 `kubectl get pod`로는 확인 불가
- 확인 방법:
  - `kubectl get events`로 이벤트 확인
  - 노드의 `kubelet` 로그 확인
  - App 로그도 함께 확인 (특히 재시작되는 경우)

---

## Pod 종료 과정 요약

1. 사용자 또는 컨트롤러가 삭제 요청
2. `kube-apiserver` → 해당 노드의 `kubelet`으로 전달
3. `kubelet` → 컨테이너 런타임 → 컨테이너 내부 App에 **SIGTERM** 전달
4. 주어진 **유예 시간(TerminationGracePeriodSeconds)** 동안 App이 정상 종료를 시도
5. 유예 시간 초과 시, 강제 종료(SIGKILL) 발생

---

## 종료 시 고려할 설정

### 1. `lifecycle.preStop`
- 종료 직전에 실행할 스크립트 설정
- 남은 트래픽 처리/DB 정리 등 가능

예:
```yaml
lifecycle:
  preStop:
    exec:
      command: ["sh", "-c", "sleep 5"]
```

---

### 2. `terminationGracePeriodSeconds`
- 기본값: **30초**
- App 종료에 더 긴 시간이 필요하다면 이 값 늘려야 함

예:
```yaml
spec:
  terminationGracePeriodSeconds: 60
```

---

## 주의할 점

- App은 SIGTERM을 감지해 **정상 종료 로직**을 꼭 구현해야 함
- DB 트랜잭션, 파일 저장, 세션 정리 등이 종료 전에 잘 처리되도록 해야 함

---

## 정리 요약

| 항목 | 설명 |
|------|------|
| 종료 원인 | 직접 삭제, 스케일 조절, 자원 정책, 노드 문제 등 |
| 종료 순서 | SIGTERM → 유예 시간 → SIGKILL |
| 확인 방법 | 이벤트 로그, kubelet 로그, App 로그 |
| 설정 | `preStop`, `terminationGracePeriodSeconds` |
| App 처리 | SIGTERM 처리 코드 구현 필수 |

```bash
# 종료 이벤트 확인
kubectl get events --sort-by=.lastTimestamp

# kubelet 로그 확인 (노드에서)
journalctl -u kubelet
```


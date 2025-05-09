# Kubernetes StatefulSet 정리
---
## StatefulSet이란?
#### StatefulSet은 **고유한 네트워크 ID와 저장소를 가진 Pod를 순서대로 생성·관리**하는 리소스입니다.
#### **상태가 있는(stateful) 애플리케이션**을 쿠버네티스에서 안정적으로 운영하기 위해 사용합니다.
---
## 주요 특징
| 특징 | 설명 |
|------|------|
| 고정된 이름 | 각 Pod에 고정된 이름 부여 (`app-0`, `app-1`, ...) |
| 고유한 네트워크 | 각 Pod는 고유한 DNS 이름을 가짐 (`app-0.myservice.default.svc.cluster.local`) |
| 고유한 볼륨 | 각 Pod는 자신만의 PersistentVolumeClaim(PVC)을 가짐 |
| 순차적 처리 | Pod 생성/삭제/스케일 동작이 **순서대로 일어남** (app-0 → app-1 → ...) |
| 안정적 재시작 | Pod가 재시작되어도 동일한 이름, 볼륨, DNS로 유지됨 |
---
## 언제 사용하나?
- 데이터베이스 (MySQL, PostgreSQL, MongoDB)
- 분산 저장 시스템 (Cassandra, Elasticsearch)
- 메시지 큐 (Kafka, RabbitMQ)
- 기타 상태를 가지는 클러스터형 애플리케이션
---
## StatefulSet 예시
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
name: web
spec:
serviceName: "web"
replicas: 3
selector:
matchLabels:
app: nginx
template:
metadata:
labels:
app: nginx
spec:
containers:
- name: nginx
image: nginx
volumeMounts:
- name: www
mountPath: /usr/share/nginx/html
volumeClaimTemplates:
- metadata:
name: www
spec:
accessModes: [ "ReadWriteOnce" ]
resources:
requests:
storage: 1Gi
```
> 위 예시에서는 `web-0`, `web-1`, `web-2`라는 이름의 Pod가 순차적으로 생성되며, 각자 고유한 PVC를 가집니다.
---
## Deployment vs StatefulSet vs DaemonSet 비교
| 항목 | Deployment | StatefulSet | DaemonSet |
|-------------|------------------|------------------|------------------|
| Pod 이름 | 랜덤 | 고정 (pod-0, ...) | 고정 |
| 순서 보장 | 없음 | 있음 | 있음 (노드 기준) |
| 저장소 | 공유 가능 | Pod별 고유 PVC | 주로 공유 안함 |
| 네트워크 ID | 랜덤 | 고정 | 고정 |
| 주요 용도 | 무상태 앱 | 상태 저장 앱 | 노드 단위 데몬 |
---
## 팁
- StatefulSet을 사용하려면 **Headless Service** (`clusterIP: None`)가 함께 필요합니다.
- `volumeClaimTemplates`가 반드시 있어야 **Pod별 볼륨을 자동 생성**합니다.
- StatefulSet은 **스케일 업/다운 시에도 데이터 정합성**을 유지하는 데 적합합니다.

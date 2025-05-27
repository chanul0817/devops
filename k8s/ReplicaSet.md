# ReplicaSet - Template, Replicas, Selector
## 1. Template & Replicas
- **Template**: Controller에서 생성할 Pod의 스펙을 정의함
- **Replicas**: 연결된 Pod 개수를 설정함
- Pod을 먼저 만들고 ReplicaSet의 `template.labels`와 `selector.matchLabels`가 일치하면 연결됨
- 일반적으로는 ReplicaSet을 먼저 만들고, 이 경우 Pod은 자동으로 생성됨 (이름 형식: `<ReplicaSet-Name>-<랜덤문자열>`)
- Pod가 삭제되면 ReplicaSet이 Template 기반으로 자동으로 다시 생성하여 Replicas 수를 유지함
> 이 실습은 Template과 Replicas 이해를 위한 연습용입니다. 실제 운영 환경에서는 **Deployment**를 주로 사용합니다.
---
### 1-1. Pod 예시
```yaml
apiVersion: v1
kind: Pod
metadata:
name: pod1
labels:
type: web
spec:
containers:
- name: container
image: kubetm/app:v1
terminationGracePeriodSeconds: 0
```
### 1-2. ReplicaSet 예시
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
name: replica1
spec:
replicas: 1
selector:
matchLabels:
type: web
template:
metadata:
name: pod1
labels:
type: web
spec:
containers:
- name: container
image: kubetm/app:v1
terminationGracePeriodSeconds: 0
```
### Replicas 변경
```bash
kubectl scale --replicas=2 replicaset replica1
```
### Controller 삭제 시 Pod 남기기
```bash
kubectl delete replicaset replica1 --cascade=orphan
```
> Pod를 수동 생성하고 Controller에 연결했을 때만 `--cascade=orphan`이 의미 있음
---
## 2. Selector
- **matchLabels**: key:value 기반의 단순 매칭
- **matchExpressions**: 조건식 기반의 복잡한 매칭 (보통 Node 스케줄링에서 많이 사용됨)
---
### 3-1. matchLabels + matchExpressions 예시
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
name: replica2
spec:
replicas: 1
selector:
matchLabels:
type: web
ver: v1
matchExpressions:
- {key: type, operator: In, values: [web]}
- {key: ver, operator: Exists}
template:
metadata:
labels:
type: web
ver: v1
location: dev
spec:
containers:
- name: container
image: kubetm/app:v1
terminationGracePeriodSeconds: 0
```
> 아래와 같이 중복 키 (`ver`)가 있는 경우에는 에러 발생
```yaml
metadata:
labels:
ver: v1
ver: v2
```
---
## 실습 후 리소스 정리
```bash
kubectl delete rs replica1 replica2
```

# 쿠버네티스 Deployment와 ReplicaSet 정리
---
## Deployment란?
- 사용자의 **의도한 상태(desired state)** 를 정의하고,
해당 상태를 유지하기 위해 **ReplicaSet을 생성하고 관리**하는 상위 리소스.
- 애플리케이션의 **롤링 업데이트**, **롤백**, **버전 관리** 등을 쉽게 할 수 있도록 지원.
- 직접적으로 파드를 관리하지 않고, ReplicaSet을 통해 간접적으로 파드를 제어함.
### 주요 기능
- 파드 수 자동 유지 및 복구
- 새로운 버전 배포 시 롤링 업데이트
- 문제 발생 시 롤백 지원 (`kubectl rollout undo` 명령 등)
---
## ReplicaSet이란?
- **지정된 수의 파드(복제본)** 가 항상 실행되도록 보장하는 쿠버네티스 컨트롤러.
- 파드가 죽거나 삭제되면 새 파드를 자동으로 생성하여 수를 유지함.
- `Deployment`가 생성될 때 내부적으로 자동 생성됨.
- 직접 사용하는 경우는 드물며, 보통 `Deployment`를 통해 관리함.
### 주요 기능
- 파드 수 복제 관리
- 레이블 셀렉터를 통해 파드 선택
---
## 구조 관계
```
[Deployment]
↓ 생성
[ReplicaSet]
↓ 생성
[Pod]
```
복사
편집
- Deployment → ReplicaSet → Pod 순으로 리소스가 생성 및 관리됨.
- 업데이트가 발생하면 새로운 ReplicaSet이 생성되어 트래픽이 순차적으로 이동함.
---
## 정리
| 항목 | Deployment | ReplicaSet |
|----------------|-----------------------------|--------------------------------|
| 역할 | 애플리케이션 배포/업데이트 관리 | 파드 복제 수 유지 |
| 업데이트 기능 | 지원 (롤링 업데이트, 롤백 등) | 미지원 (고정된 파드 복제 유지만 함) |
| 생성 방식 | 사용자가 직접 생성 | Deployment에 의해 자동 생성 |
| 직접 사용 빈도 | 높음 | 낮음 (Deployment를 통해 관리) |
---
## 예시 YAML
### Deployment 예시
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: nginx-deployment
spec:
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
image: nginx:1.25
```

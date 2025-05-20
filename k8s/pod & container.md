## 1. Pod & Container

- Pod는 쿠버네티스에서 가장 작은 배포 단위
- Pod IP는 생성 시 부여되고 삭제 후 재생성 시 변경됨
- Pod IP는 클러스터 내부에서만 접근 가능
  - 외부 접근은 Service를 통해 가능
- Pod 내부에 여러 Container 구성 가능
  - 서로 localhost로 통신 가능
- 하나의 Pod 내 Container끼리는 포트 충돌이 나면 에러 발생

---

## 2. Deployment

- Pod의 수를 관리하기 위한 상위 개념 리소스
- Deployment는 ReplicaSet을 통해 Pod 수를 유지
- `spec.replicas` 값을 조정해 스케일링 가능
- 기존 Pod가 있으면 `kubectl apply`로 업데이트 가능

---

## 3. Label

- 리소스에 key:value 형태로 붙이는 메타데이터
- 용도:
  1. 리소스를 분류하고 구분
  2. Service나 Selector 등에서 리소스 선택에 사용
- 하나의 리소스에 여러 Label 부착 가능

---

## 4. Service

- 클러스터 내부 또는 외부에서 Pod에 접근할 수 있도록 해주는 추상화
- Selector를 통해 Label과 일치하는 Pod에 연결됨
- Pod는 죽었다가 재생성되면 IP가 바뀌므로 Service로 고정된 접근 필요

---

## 5. Node Scheduling

- 쿠버네티스는 Pod를 적절한 Node에 자동으로 스케줄링
- 기본 기준: Node의 자원 상태 (메모리, CPU 등)
- 사용자가 `nodeSelector`로 직접 지정도 가능
- 리소스 설정 (`resources.requests` / `resources.limits`) 통해 제어 가능
  - memory: limit 초과 시 OOM 발생
  - cpu: limit 이상 사용 불가

---

## 6. Pod 종료 (Termination)

- Pod 종료 원인:
  - 사용자가 직접 삭제
  - Deployment 등 컨트롤러가 Replica 줄임
  - 자원 부족 또는 관리 정책 (QoS, Priority 등)
  - Node 장애 혹은 `kubectl drain`
  - Job 완료로 인해 자동 삭제

- 종료 시 처리:
  - kubelet이 SIGTERM → Application으로 전달
  - Application은 SIGTERM 수신 후 graceful shutdown 구현 필요

- 설정 고려:
  - `lifecycle.preStop`: 종료 전에 처리할 작업 정의 가능
  - `terminationGracePeriodSeconds`: 기본 30초, App 종료 시간 고려해 늘릴 수 있음

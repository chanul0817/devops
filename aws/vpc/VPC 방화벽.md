# VPC 방화벽: Security Group vs Network ACL

AWS VPC에서 트래픽을 통제하고 제어하는 대표적인 방화벽 서비스는 Security Group(보안 그룹)과 Network ACL(네트워크 액세스 제어 목록)입니다.

---

## Security Group (보안 그룹)

- **적용 범위**: 인스턴스(ENI) 단위
- **역할**: 인스턴스의 인바운드(외부→인스턴스), 아웃바운드(인스턴스→외부) 트래픽을 제어하는 가상 방화벽
- **특징**
  - 허용(Allow) 규칙만 생성 가능 (Deny 불가)
  - 인바운드/아웃바운드 규칙을 각각 설정
  - 기본적으로 모든 아웃바운드는 허용
  - 인스턴스당 최대 5개 보안 그룹 동시 적용 가능
  - 상태 저장(Stateful): 한 번 허용된 트래픽은 응답 시 별도 규칙 없이 허용
  - EC2, RDS, ELB 등 ENI를 갖는 모든 리소스에 적용 가능
  - 등록된 모든 규칙을 평가하여 트래픽 허용

- **실전 예시**
  - 22(SSH), 80(HTTP), 443(HTTPS) 포트만 허용
  - 특정 IP만 허용하거나, 내 IP만 허용 가능

---

## Network ACL (네트워크 액세스 제어 목록)

- **적용 범위**: 서브넷 단위
- **역할**: 서브넷의 인바운드/아웃바운드 트래픽을 제어하는 방화벽
- **특징**
  - 허용(Allow) 및 거부(Deny) 규칙 모두 생성 가능
  - 규칙 번호(낮은 숫자 우선) 순서대로 평가
  - 인바운드/아웃바운드 각각 최대 20개 규칙(기본값)
  - 여러 서브넷에 동일한 NACL 적용 가능, 한 서브넷엔 한 NACL만 연결
  - 상태 비저장(Stateless): 인바운드/아웃바운드 각각 별도 규칙 필요
  - 기본적으로 모든 트래픽 거부(사용자 생성 NACL 기준)
  - IP, 포트, 프로토콜 단위로 세밀한 제어 가능

- **실전 예시**
  - 100번 규칙: 80포트 Allow, 101번 규칙: 80포트 Deny → 100번 규칙이 우선 적용
  - 악의적 IP만 Deny, 나머지는 Allow

---

## 주요 차이점 비교

| 항목                   | Security Group                | Network ACL                    |
|------------------------|-------------------------------|-------------------------------|
| 적용 범위              | 인스턴스(ENI) 단위            | 서브넷 단위                   |
| 규칙 유형              | 허용(Allow)만 가능            | 허용(Allow), 거부(Deny) 모두 가능 |
| 상태 저장              | 상태 저장(Stateful)           | 상태 비저장(Stateless)        |
| 규칙 평가 방식         | 모든 규칙 평가                | 번호순(낮은 숫자 우선)        |
| 기본 동작              | 아웃바운드 모두 허용          | 모든 트래픽 거부(사용자 생성 NACL) |
| 적용 가능 개수         | 인스턴스당 최대 5개           | 서브넷당 1개                  |

---

## 함께 사용하는 방법

- **NACL**: 서브넷 레벨에서 가장 먼저 트래픽을 필터링(특정 IP 차단 등)
- **Security Group**: 인스턴스 레벨에서 포트/프로토콜 등 세부 허용
- 두 계층을 조합해 다단계 보안 구성 권장

---

## 참고 사항

- Security Group은 EC2, RDS, ELB 등 다양한 리소스에 적용 가능
- NACL은 모든 서브넷에 기본적으로 하나씩 존재, 필요시 추가 생성 및 적용
- NACL은 인바운드/아웃바운드 모두 규칙 필요(Stateless)
- Security Group은 인바운드만 허용하면 응답 트래픽은 자동 허용(Stateful)

---

> Security Group과 Network ACL은 VPC 보안을 위한 핵심 방화벽 서비스입니다. 각각의 특징과 동작 원리를 이해하고, 상황에 맞게 조합하여 사용하는 것이 중요합니다.

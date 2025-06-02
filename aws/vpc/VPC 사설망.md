# VPC 사설망 외부 통신 핵심 정리

## 1. Public Subnet과 Private Subnet

- Public Subnet: 인터넷 게이트웨이(IGW)와 연결, 외부에서 접근 가능
- Private Subnet: 외부 인터넷 차단, 내부 서비스(예: RDS)용

---

## 2. Private Subnet에서 외부 통신 방법

### NAT Gateway

- Private Subnet 인스턴스가 외부 인터넷으로 나갈 때 사용하는 네트워크 주소 변환 장치
- Public Subnet에 생성, Elastic IP 할당 필요
- 내부에서 외부로만 통신 가능 (외부에서 내부로는 불가)
- 보안과 외부 통신을 모두 만족
- 요금 발생: 시간당 요금 + 데이터 처리 요금

### NAT Instance

- EC2 인스턴스를 NAT 역할로 사용 (구식 방식)
- 비용 절감 가능 (프리티어 EC2 사용 시 무료)
- 고가용성 보장 X, 인스턴스 꺼지면 NAT 기능 중단
- 보안그룹 영향 받음, 관리 필요

| 항목         | NAT Gateway         | NAT Instance         |
|--------------|---------------------|----------------------|
| 타입         | AWS 관리형 서비스   | EC2 인스턴스         |
| 가용성       | 고가용성            | 단일 인스턴스        |
| 비용         | 비쌈                | 저렴/무료 가능       |
| 관리         | 자동                | 직접 관리 필요       |

---

## 3. Bastion Host

- Public Subnet에 위치한 EC2 인스턴스
- 외부에서 SSH로 Bastion Host에 접속 → Bastion Host에서 Private Subnet 인스턴스로 SSH 접속
- Private Subnet 인스턴스에는 Public IP가 없으므로 직접 외부 접속 불가
- 보안성 높음, 관리자는 Bastion Host를 통해서만 내부에 접근

### Bastion Host 구성 순서

1. VPC 및 Public/Private Subnet 구성
2. Public EC2(Bastion Host) 생성 (Public IP 할당)
3. Private EC2 인스턴스 생성 (Private Subnet)
4. Bastion Host에서 Private EC2로 SSH 접속

### 보안 그룹 설정

- Public 인스턴스: 0.0.0.0/0 (모두 허용) 또는 내 IP만 허용
- Private 인스턴스: Bastion Host의 보안 그룹만 허용

---

## 4. 실습/운영 팁

- Private Subnet에서 인터넷 사용 필요 시, NAT Gateway를 반드시 Public Subnet에 생성
- Private Subnet의 라우팅 테이블에 0.0.0.0/0 → NAT Gateway로 라우팅 추가
- Bastion Host는 외부에서 내부로 접속하는 용도, NAT Gateway는 내부에서 외부로 나가는 용도
- Bastion Host에서 Private 인스턴스 접속 시, Private 키페어 파일 필요 (권한 600으로 변경)
- MobaXterm 등 SSH Gateway 기능 활용 시 Bastion Host 경유 자동화 가능

---

## 5. 요약

- Private Subnet의 외부 통신: NAT Gateway(요즘 방식, 유료) / NAT Instance(구식, 저렴)
- Private Subnet의 외부 접속: Bastion Host(중간 다리 역할, 보안성↑)
- Public/Private Subnet, NAT Gateway, Bastion Host 조합으로 보안성과 외부 통신 모두 확보

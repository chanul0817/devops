# AWS NAT 인프라 정리

## 1. NAT란?
- NAT(Network Address Translation)는 Private Subnet의 리소스가 외부 인터넷에 직접 노출되지 않고, 내부 IP를 공인 IP로 변환해 외부와 통신할 수 있게 해주는 기술
- AWS에서는 NAT Gateway와 NAT Instance 두 가지 방식 제공

---

## 2. NAT Gateway

- AWS에서 제공하는 완전관리형 NAT 서비스
- Public Subnet에 위치, Elastic IP 할당 필수
- Private Subnet의 라우팅 테이블에서 0.0.0.0/0 트래픽을 NAT Gateway로 지정
- 고가용성(HA) 및 자동 확장 지원, 최대 100Gbps 대역폭
- 유지보수 필요 없음, AWS가 관리
- 보안그룹 직접 적용 불가(뒤쪽 리소스에만 적용)
- **비용**: 서울 리전 기준 시간당 약 0.059달러 + 데이터 전송 요금, 한 달 42달러 이상
- 고성능, 고가용성이 필요한 서비스에 권장

---

## 3. NAT Instance

- EC2 인스턴스를 NAT 역할로 직접 설정
- Public Subnet에 위치, Elastic IP 할당 필요
- Private Subnet의 라우팅 테이블에서 0.0.0.0/0 트래픽을 NAT Instance로 지정
- 인스턴스가 꺼지면 NAT 기능 중단, 장애 시 수동 복구 필요
- 보안그룹 적용 가능, 포트포워딩 지원, Bastion Host 겸용 가능
- 인스턴스 타입에 따라 대역폭 제한, 고성능/고가용성에는 부적합
- **비용**: t4g.nano 기준 한 달 약 3~4달러(프리티어 사용 시 무료)
- 저렴하게 간단한 NAT 환경이 필요할 때 적합

---

## 4. 주요 비교

| 항목            | NAT Gateway                | NAT Instance                |
|-----------------|----------------------------|-----------------------------|
| 관리            | AWS 완전관리형              | 직접 관리 필요              |
| 가용성          | 고가용성(HA), 자동 확장     | 단일 인스턴스, 장애 시 중단 |
| 대역폭          | 최대 100Gbps                | 인스턴스 타입에 따라 다름   |
| 보안그룹        | 직접 적용 불가              | 직접 적용 가능              |
| 포트포워딩      | 불가                        | 가능                        |
| Bastion 겸용    | 불가                        | 가능                        |
| 비용            | 높음 (월 42달러 이상)       | 저렴 (월 3~4달러)           |
| 유지보수        | 필요 없음                   | 직접 패치/모니터링 필요     |

---

## 5. 실전 구축 팁

- NAT Gateway/Instance는 반드시 Public Subnet에 위치해야 함
- Private Subnet의 라우팅 테이블에서 0.0.0.0/0 경로를 NAT Gateway/Instance로 지정
- NAT Instance는 Source/Destination Check를 반드시 비활성화해야 함[3]
- 비용이 부담되면 필요할 때만 NAT Gateway를 생성하거나, 오픈소스(fck-nat 등) NAT Instance 활용 고려[2]

---

## 6. 결론

- 고가용성, 고성능, 자동화가 필요하면 NAT Gateway 사용 권장
- 비용 절감, 직접 관리, 단순 환경이면 NAT Instance도 대안
- 상황에 따라 적절한 NAT 방식을 선택해 네트워크 설계

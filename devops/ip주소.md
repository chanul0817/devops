# IP 주소 정리

## 1. IP란?
- IP(Internet Protocol): 인터넷에 연결된 장비를 식별하는 고유 주소
- 예시: 192.168.0.1 (32비트 이진수: 11000000.10101000.00000000.00000001)
- IPv4 주소 개수: 2³² ≈ 43억 개

---

## 2. IP 주소 구성
- **Network ID:** 같은 네트워크 그룹 구분
- **Host ID:** 네트워크 내 개별 장비 식별
- 비유: 지역번호(Network ID) + 개인번호(Host ID)

---

## 3. IP 클래스

| 클래스 | 시작 IP         | 끝 IP             | 서브넷 마스크      | 호스트 수   | 설명           |
|--------|----------------|-------------------|-------------------|-------------|----------------|
| A      | 1.0.0.0        | 127.255.255.255   | 255.0.0.0         | 약 1,670만  | 대규모용       |
| B      | 128.0.0.0      | 191.255.255.255   | 255.255.0.0       | 약 65,000   | 중규모용       |
| C      | 192.0.0.0      | 223.255.255.255   | 255.255.255.0     | 254         | 소규모용       |
| D      | 224.0.0.0      | 239.255.255.255   | -                 | -           | 멀티캐스트용   |
| E      | 240.0.0.0      | 255.255.255.255   | -                 | -           | 실험/예약      |

---

## 4. 네트워크 주소 & 브로드캐스트 주소
- **네트워크 주소:** 호스트 ID가 전부 0 (예: 192.168.10.0)
- **브로드캐스트 주소:** 호스트 ID가 전부 1 (예: 192.168.10.255)
- **사용 가능한 IP:** 192.168.10.1 ~ 192.168.10.254

---

## 5. 서브넷 (Subnet)
- 하나의 IP 블록을 더 작은 단위로 분할
- A/B/C 클래스만으로는 비효율적 → 서브넷 등장

---

## 6. 서브네팅과 서브넷 마스크
- **서브넷 마스크:** 네트워크/호스트 구분 기준 (예: 255.255.255.0 = 11111111.11111111.11111111.00000000)
- **CIDR 표기:** /n 방식으로 서브넷 마스크 표현 (예: 192.168.0.1/24 → 255.255.255.0)

---

## 7. 정리

| 개념             | 설명                                  |
|------------------|---------------------------------------|
| IP 주소          | 장비의 고유 주소                      |
| Network ID       | 같은 네트워크 그룹                    |
| Host ID          | 네트워크 내 개별 장비                 |
| 서브넷           | 네트워크를 더 작게 나눈 것            |
| 서브넷 마스크    | 네트워크/호스트 구분 기준             |
| CIDR (/24 등)    | 서브넷 마스크를 간단히 표현한 방식    |
| 네트워크 주소    | 호스트 ID가 전부 0                    |
| 브로드캐스트 주소| 호스트 ID가 전부 1                    |

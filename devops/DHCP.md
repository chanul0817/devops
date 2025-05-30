# DHCP 정리
---
### 1. DHCP란?
- Dynamic Host Configuration Protocol의 줄임말
- IP 주소, 서브넷 마스크, 게이트웨이, DNS 같은 네트워크 정보를 **자동으로 할당**해주는 프로토콜
- IP 주소를 수동으로 설정할 필요 없이 자동으로 받게 해줌
---
### 2. 왜 필요한가?
- 네트워크에 새로운 기기가 들어올 때마다 IP를 직접 설정하는 것은 번거로움
- DHCP 서버가 자동으로 IP를 관리하면 **충돌 방지, 관리 편의성** 증가
---
### 3. 작동 방식 (4단계)
1. **DHCP Discover**
- 클라이언트가 "IP 주소 줄 사람 있어요?"라고 브로드캐스트로 물어봄
2. **DHCP Offer**
- DHCP 서버가 "이 IP 줄게요" 하고 제안
3. **DHCP Request**
- 클라이언트가 "그 IP 받을게요!" 하고 요청
4. **DHCP Acknowledgement (ACK)**
- 서버가 "확인했어요. 사용하세요."라고 승인
---
### 4. 임대(Lease) 개념
- IP는 **영구적으로 주어지는 게 아니라 일정 시간만 사용**
- 시간이 지나면 다시 갱신하거나 반납해야 함
---
### 5. DHCP 서버 예시
- 공유기 (가정용 라우터에 내장됨)
- 사무실의 네트워크 서버
- 클라우드 인프라 (AWS, GCP 등)에서도 내부적으로 사용
---
### 6. 장점
- IP 자동 할당 → 설정 실수 줄어듬
- 장비 추가/삭제가 쉬움
- 네트워크 관리 자동화
---
### 7. 단점
- DHCP 서버가 없으면 IP를 못 받음
- 보안상 MAC 주소 위조 등으로 IP 탈취 가능성 있음
---
### 8. 정적 IP vs DHCP
| 구분 | DHCP | 정적 IP |
|-------------|------------------------|--------------------------------|
| IP 부여 방식 | 자동 할당 | 수동 설정 |
| 관리 | 편리함 | 유지보수 어려움 |
| 사용 사례 | 대부분의 일반 장비 | 서버, 프린터, 네트워크 장비 등 |

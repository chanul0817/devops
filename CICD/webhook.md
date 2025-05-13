## Webhook 정리
---
### 1. Webhook이란?
- **이벤트가 발생했을 때**, 서버가 **정해진 URL로 자동으로 HTTP 요청을 보내는 방식**
- "변화 생기면 내가 알려줄게" → **수동 polling 대신 알림 방식**
- 예를 들어 GitHub에서 푸시가 일어나면, 내 서버에 바로 알려주는 게 webhook
---
### 2. 동작 구조
```
[이벤트 발생 시스템] ──(HTTP 요청 POST)──> [내 서버의 특정 URL]
```
예시:
- GitHub → 내 EC2 `/github/webhook` 으로 POST 요청
- Stripe 결제 완료 → 내 서버 `/payment/webhook`으로 POST 요청
---
### 3. 구성 요소
| 항목 | 설명 |
|--------------|------|
| 이벤트 소스 | GitHub, Stripe, Slack, Toss 등 외부 서비스 |
| 수신 URL | 내가 지정한 endpoint (예: `https://myapp.com/webhook`) |
| HTTP 방식 | 보통 `POST` 요청 |
| 데이터 형식 | 보통 `JSON` |
| 인증 방식 | 보안 토큰, 시그니처 해시 등으로 검증 |
---
### 4. EC2에서 Webhook 받기 예시 (Spring Boot)
```java
@PostMapping("/webhook")
public ResponseEntity<String> receiveWebhook(@RequestBody String payload) {
System.out.println("웹훅 데이터: " + payload);
return ResponseEntity.ok("받음");
}
```
---
### 5. 실제 사용 예시
| 서비스 | 이벤트 예시 |
|------------|----------------------------------|
| GitHub | push, pull request, issue 생성 등 |
| Stripe | 결제 성공/실패 |
| Slack | 채팅 메시지 수신 |
| IFTTT | IoT 디바이스 연동 |
---
### 6. Webhook vs Polling
| 비교 항목 | Webhook | Polling |
|------------|----------------------------------|-------------------------------|
| 방식 | 이벤트 발생 시 서버에 알림 | 주기적으로 상태를 확인 |
| 반응 속도 | 빠름 (실시간) | 느릴 수 있음 |
| 효율성 | 리소스 효율적 | 리소스 낭비 가능 |
| 설정 | 이벤트 소스에서 등록 필요 | 클라이언트에서 구현 |
---
### 7. Webhook 개발할 때 팁
- HTTPS 꼭 사용하기 (보안)
- 받은 데이터 **로그 남기기**
- 요청 인증/검증 로직 넣기 (시크릿 키 또는 해시)
- 처리 오래 걸리면 비동기 처리 권장 (예: 큐에 넣기)
- 실패했을 경우 재시도 로직 구현 고려
---

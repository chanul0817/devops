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

실제로 GitHub webhook으로 배포 걸 때는 대충 이런 흐름이 많음

1. GitHub `push` 이벤트 수신
2. 브랜치가 `main`이나 `daily` 같은 대상 브랜치인지 확인
3. 바로 서버에서 `git pull` 하지 말고 배포 스크립트나 CI 잡 호출
4. 응답은 일단 빨리 `200 OK` 주고, 실제 배포는 뒤에서 처리
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

### 8. 받을 때 주의할 점
- webhook은 외부에서 내 서버로 들어오는 요청이라서 아무 요청이나 믿으면 안 됨
- GitHub 같은 서비스는 secret을 같이 설정하고, 서버에서는 signature를 검증하는 식으로 처리
- 응답이 너무 늦으면 상대 서비스가 실패로 보고 다시 보낼 수 있음
- 같은 이벤트가 두 번 올 수도 있으니, 이벤트 id를 저장해 중복 처리 방지
- 배포 트리거로 쓸 때는 바로 배포하지 말고 브랜치, 권한, 테스트 결과를 먼저 확인

### 9. 운영하다 보면 보는 문제

- GitHub webhook 설정은 성공으로 뜨는데 서버 로그가 없으면, 보안 그룹이나 Nginx 라우팅부터 확인하는 게 빠름
- secret 검증이 계속 실패하면 payload를 가공하기 전에 raw body 기준으로 서명 검증하는지 봐야 함
- 배포 서버 한 대에 webhook을 직접 받게 해두면, 배포 중 timeout 나면서 같은 이벤트가 다시 들어오는 경우도 있어서 큐나 작업 잠금이 있으면 편함
- Jenkins나 GitHub Actions를 붙여두는 구조라면, webhook 서버는 무거운 작업 하지 말고 "이벤트 확인 후 전달" 역할만 하는 쪽이 관리가 쉬움

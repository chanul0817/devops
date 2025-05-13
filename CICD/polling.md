# EC2에서의 Polling 기법 정리
---
### 1. Polling이란?
- 내가 서버를 만든 다음 github에 계속 request으로 코드가 push 되었는지 물어봅니다
- 서버나 장치에서 **변화를 감지하기 위해 주기적으로 상태를 확인**하는 기법
- 말 그대로 "계속 물어보기"
- 예: "새로운 요청 왔나요?"를 계속 묻는 방식
---
### 2. EC2에서 Polling은 언제 쓰나?
| 사용 사례 예시 |
|----------------|
| 백엔드 서버가 DB나 큐를 주기적으로 확인 |
| 특정 폴더에 새로운 파일이 올라왔는지 체크 |
| SQS 같은 메시지 큐에서 메시지 존재 여부 확인 |
| IoT 기기 상태를 5초마다 체크 |
---
### 3. Polling 방식 예시 (Bash + Curl)
```bash
#!/bin/bash
while true; do
curl -s http://localhost:8080/health
echo "상태 확인됨 $(date)"
sleep 10
done
```
- 10초마다 서버가 살아있는지 확인하는 예제
- EC2에서 `.sh`로 만들어 `nohup` 등으로 백그라운드 실행 가능
---
### 4. Polling의 단점
- **비효율적**: 필요 없을 때도 계속 요청함
- **지연 있음**: 변화가 생겨도 바로 알지 못함 (폴링 주기에 따라 늦게 탐지됨)
- **리소스 낭비**: 네트워크, CPU를 불필요하게 사용할 수 있음
---
### 5. Long Polling (개선된 Polling)
- 서버가 즉시 응답하지 않고, **데이터가 생길 때까지 기다렸다가 응답**
- 일반 polling보다 리소스를 덜 쓰고, 실시간성 높음
- AWS SQS에서는 기본적으로 long polling을 지원
---
### 6. 대안 기법
| 기법 | 설명 |
|-------------|------|
| Webhook | 변화가 생기면 서버가 클라이언트에게 알려줌 |
| WebSocket | 서버와 클라이언트가 **양방향으로 실시간 통신** |
| Event-driven | 이벤트가 발생할 때만 처리. 리소스 효율적 |
---
### 7. EC2에서 polling 돌릴 때 실전 팁
- `while true; do ...; sleep n; done` 구조 자주 사용
- 배치 작업이면 `cron`과 병행 가능
- 서버 죽지 않게 하려면 `nohup`이나 `screen`, `tmux`로 실행
- 너무 짧은 간격으로 polling 하지 말기 (서버 부하)
---
### 8. 간단한 polling bash 예시 (파일 존재 확인)
```bash
#!/bin/bash
while true; do
if [ -f /home/ubuntu/uploaded.txt ]; then
echo "파일 발견!"
# 처리 로직 작성
rm /home/ubuntu/uploaded.txt
fi
sleep 5
done
```
---

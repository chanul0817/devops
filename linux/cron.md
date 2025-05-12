## cron (크론) - 주기적으로 명령어 실행하는 리눅스 도구
---
### 1. 기본 개념
- `cron`은 백그라운드에서 돌아가는 데몬
- `crontab`은 사용자가 설정하는 스케줄 테이블
---
### 2. crontab 명령어
```bash
crontab -e # 크론 설정 편집
crontab -l # 현재 등록된 크론 목록 확인
crontab -r # 크론 전체 삭제
```
---
### 3. crontab 문법
```
* * * * * command_to_execute
│ │ │ │ │
│ │ │ │ └─ 요일 (0~7, 일요일은 0 또는 7)
│ │ │ └─── 월 (1~12)
│ │ └───── 일 (1~31)
│ └─────── 시 (0~23)
└───────── 분 (0~59)
```
---
### 4. 예시
```bash
0 9 * * * /home/ubuntu/backup.sh
```
- 매일 오전 9시에 `/home/ubuntu/backup.sh` 실행
```bash
*/5 * * * * curl -s http://localhost:8080/health >> /home/ubuntu/health.log
```
- 5분마다 서버 상태 체크하고 로그 저장
---
### 5. 로그 확인
- 대부분 시스템 로그에 기록됨
```bash
cat /var/log/syslog | grep CRON # Ubuntu
journalctl -u cron.service # systemd 기반
```
---
### 6. 주의사항
- 경로는 **절대경로**로 써야 함
- 실행 권한 (`chmod +x`) 꼭 확인
- 환경변수(PATH 등)가 제한적이므로 명령어가 실패할 수 있음

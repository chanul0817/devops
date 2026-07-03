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

---

### 7. 운영 환경에서 자주 발생하는 문제

#### 1) PATH 문제

cron은 로그인 셸과 다르게 최소한의 환경변수만 가지고 실행됩니다.
터미널에서는 잘 되던 명령어가 cron에서 실패하면 명령어의 전체 경로를 사용합니다.

```bash
* * * * * /usr/bin/python3 /home/ubuntu/scripts/collect.py
```

필요하면 crontab 상단에 PATH를 직접 지정할 수 있습니다.

```bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

#### 2) 로그가 없어서 실패 원인을 모르는 경우

표준 출력과 표준 에러를 파일로 남기면 원인 파악이 쉬워집니다.

```bash
0 2 * * * /home/ubuntu/backup.sh >> /var/log/backup.log 2>&1
```

- `>>` : 기존 로그 뒤에 추가
- `2>&1` : 표준 에러를 표준 출력과 같은 곳에 기록

#### 3) 작업이 중복 실행되는 경우

이전 작업이 끝나기 전에 다음 cron이 시작되면 백업, 배치, 동기화 작업이 중복 실행될 수 있습니다.
`flock`을 사용하면 같은 작업이 동시에 여러 번 실행되는 것을 막을 수 있습니다.

```bash
*/10 * * * * /usr/bin/flock -n /tmp/batch.lock /home/ubuntu/batch.sh >> /var/log/batch.log 2>&1
```

- `-n` : 이미 잠금이 있으면 기다리지 않고 바로 종료
- `/tmp/batch.lock` : 중복 실행 여부를 판단하는 잠금 파일

#### 4) 시간대 확인

서버 시간대가 UTC인지 KST인지에 따라 실행 시간이 달라질 수 있습니다.

```bash
timedatectl
date
```

운영 서버에서는 cron 등록 전에 실제 서버 시간대를 먼저 확인하는 습관이 중요합니다.

#### 5) crontab 수정 전 백업

`crontab -e`로 바로 수정하다가 기존 설정을 지우면 복구가 귀찮아집니다.
수정 전에는 현재 내용을 파일로 빼두는 게 좋습니다.

```bash
crontab -l > crontab.backup
crontab -l
```

수정 후에는 등록이 잘 됐는지 다시 확인합니다.

```bash
crontab -l
```

### cron에서 명령이 안 먹을 때

- 터미널에서는 되는데 cron에서는 안 되면 PATH부터 확인
- 스크립트 안에서는 가능하면 절대경로를 쓰는 게 안전함
- 로그 파일로 stdout, stderr를 남겨야 원인 찾기가 쉬움


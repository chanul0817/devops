# EC2 외부 접속 방법 요약

## 1. PuTTY (윈도우 SSH 접속)

**준비물**
- putty.exe (SSH 접속용)
- puttygen.exe (.pem → .ppk 변환용)

**접속 순서**
1. puttygen 실행 → Load 클릭 → .pem 키파일 선택 → Save private key → .ppk 저장
2. putty 실행
3. Host Name에 EC2 퍼블릭 IPv4 입력
4. 좌측 SSH → Auth → Private key file에 .ppk 파일 지정
5. Data 탭에서 Auto-login username에 ec2-user(아마존 리눅스) 또는 ubuntu(우분투) 입력
6. Open 클릭하여 접속

## 2. FileZilla (SFTP 파일 전송)

1. 사이트 관리자 열기
2. 프로토콜: SFTP
3. 호스트: EC2 퍼블릭 IP
4. 포트: 22
5. 키파일: Puttygen에서 변환한 .ppk 지정
6. 연결 클릭 → 파일 전송 가능

## 3. Cmder (윈도우 터미널)

1. .pem 키파일 있는 폴더로 이동
2. 권한 설정 (접속 오류시):
   chmod 400 키페어.pem
3. SSH 명령어 입력:
   ssh -i "키페어.pem" ec2-user@퍼블릭IP

## 4. Git Bash

- Cmder와 동일하게 ssh 명령어 실행

## 5. MobaXterm (SSH + SFTP 통합)

1. Session 클릭 → SSH 선택
2. Remote Host에 퍼블릭 IP 입력
3. Specify username에 ec2-user(아마존 리눅스) 또는 ubuntu(우분투) 입력
4. Use private key → .pem 키 지정
5. OK 클릭 → 접속 완료 (터미널+파일 브라우저 제공)

## 6. Windows 인스턴스 (RDP)

1. EC2 인스턴스에서 RDP 클릭
2. 퍼블릭 IP 입력
3. Username: Administrator
4. Password: AWS 콘솔에서 .pem 키로 해독
5. RDP로 접속

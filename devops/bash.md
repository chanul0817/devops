# Bash
---
### 1. Bash란?
- **Bourne Again SHell**의 줄임말
- 리눅스와 macOS에서 기본적으로 사용하는 **셸(Shell)** 중 하나
- 사용자가 명령어를 입력하면 OS가 실행할 수 있도록 해주는 **명령어 해석기**
---
### 2. 셸(Shell)이란?
- 커널과 사용자 사이를 이어주는 **인터페이스**
- 사용자의 명령어를 해석하고 실행
- 다양한 셸: sh, bash, zsh, ksh, fish 등
---
### 3. Bash 특징
- **명령어 실행** 기능
- **스크립트 작성** 가능 (`.sh` 파일)
- **변수, 조건문, 반복문, 함수** 등 프로그래밍 요소 사용 가능
- 리눅스 서버에서 **자동화**에 매우 중요
---
### 4. 기본 명령어 예시
```bash
echo "Hello, world!"
cd /home/ubuntu
ls -al
```
---
### 5. 스크립트 예시 (`hello.sh`)
```bash
#!/bin/bash
echo "Bash 시작!"
name="성우"
echo "안녕하세요, $name 님!"
```
실행 방법:
```bash
chmod +x hello.sh
./hello.sh
```
---
### 6. bash 사용 환경
- 대부분의 리눅스 배포판에서 기본 제공
- macOS 터미널도 bash 사용 가능 (요즘은 기본이 zsh로 바뀌었지만 여전히 bash 사용 가능)
- Windows에서도 WSL 설치 시 bash 사용 가능
---
### 7. bash 프로그래밍 요소
- 변수 선언: `name="성우"`
- 조건문:
```bash
if [ $age -ge 20 ]; then
echo "성인입니다"
fi
```
- 반복문:
```bash
for i in 1 2 3; do
echo $i
done
```
---
### 8. 용도
- 서버 관리 자동화
- 배포 스크립트 (`deploy.sh`, `start.sh`)
- 설치 자동화
- 백업 및 모니터링 스크립트 등
---
### 9. bash 파일 확장자
- 보통 `.sh` 사용하지만, 확장자는 필수가 아님

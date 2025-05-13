# EC2에 있는 파일 꺼내는 방법 정리

##  1. MobaXterm을 사용할 때 (GUI)

- EC2에 SSH로 접속하면, 왼쪽에 **"SFTP 브라우저 창"** 자동 생성됨
- 이 창에서 EC2 내의 파일을 **드래그해서 로컬로 꺼낼 수 있음**
- 또는 **오른쪽 클릭 > Download** 하면 다운로드 가능

### 예시 흐름
1. MobaXterm 실행
2. Sessions > SSH > EC2 접속
3. 접속 후 왼쪽 SFTP 패널에서 `/home/ubuntu/test.txt` 찾아서 내 PC로 드래그

---

## 2. 일반 터미널에서 `scp` 사용하기 (CLI)

### EC2 → 로컬로 파일 꺼내오기

```bash
scp -i [pem키경로] ubuntu@[EC2 IP주소]:[EC2상의 파일경로] [내PC에 저장할 경로]
```

#### 예시

```bash
scp -i ~/Downloads/my-key.pem ubuntu@13.124.123.123:/home/ubuntu/test.txt ./test.txt
```

- 이 명령어는 EC2에 있는 `test.txt` 파일을 **내 현재 디렉토리(./)** 로 복사함

### 로컬 → EC2로 파일 보내기

```bash
scp -i [pem키경로] [내 로컬 파일 경로] ubuntu@[EC2 IP주소]:[EC2의 저장 경로]
```

#### 예시

```bash
scp -i ~/Downloads/my-key.pem ./localfile.txt ubuntu@13.124.123.123:/home/ubuntu/
```

---

## 참고
- `scp`는 **로컬 터미널에서 실행**해야 함 (EC2 내부에서는 안 됨)
- EC2 인바운드 규칙에 SSH(22번 포트) 허용돼 있어야 함
- `.pem` 파일 권한이 너무 열려 있으면 `chmod 400`으로 권한 설정해야 함

```bash
chmod 400 my-key.pem
```

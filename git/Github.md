# Github

### **Git 원격 저장소 연동 및 기본 사용법**

### 1. **GitHub란?**

GitHub는 Git 기반의 온라인 원격 저장소 서비스로, 협업과 코드 관리를 지원합니다.

- 코드 백업 및 공유
- 팀 협업 및 코드 리뷰
- 오픈소스 기여와 학습 커뮤니티 참여

### 2. **원격 저장소 설정 및 연결**

1. **GitHub 저장소 생성**
    - [GitHub 사이트](https://github.com/)에서 로그인 후 `New` 버튼 클릭.
    - 저장소 이름 설정, 공개/비공개 여부 선택 후 `Create Repository` 클릭.
2. **로컬 저장소와 연결**
    
    ```bash
    bash
    코드 복사
    # 원격 저장소 추가
    $ git remote add origin <repository_url>
    
    # 설정 확인
    $ git remote -v
    
    ```
    

### 3. **파일 업로드**

1. **변경사항 추가 및 커밋**
    
    ```bash
    bash
    코드 복사
    $ git add .  # 모든 변경 파일 추가
    $ git commit -m "커밋 메시지"
    
    ```
    
2. **원격 저장소로 Push**
    
    ```bash
    bash
    코드 복사
    $ git push -u origin master  # origin 저장소의 master 브랜치로 업로드
    
    ```
    

### 4. **원격 저장소에서 변경사항 동기화**

1. **Pull**
    
    ```bash
    bash
    코드 복사
    $ git pull origin master  # 원격 저장소 변경사항을 병합
    
    ```
    
    - 원격 변경사항을 가져오며, 충돌이 발생하면 수동으로 해결 후 커밋 필요.
2. **Fetch**
    
    ```bash
    bash
    코드 복사
    $ git fetch origin  # 원격 저장소 정보를 가져오지만 병합은 하지 않음
    
    ```
    

### 5. **원격 저장소 복제**

```bash
bash
코드 복사
$ git clone <repository_url>  # 저장소를 로컬로 복사

```

### 6. **협업 시 주의사항**

- Pull과 Fetch를 사용해 작업 전 최신 상태 유지.
- 작업 전 항상 `git status`로 현재 상태 확인.
- 변경 후 꼭 `commit`하고 Push.

### 7. **기타 명령어**

- 원격 저장소 이름 변경
    
    ```bash
    bash
    코드 복사
    $ git remote rename 기존이름 새이름
    
    ```
    
- 원격 저장소 삭제
    
    ```bash
    bash
    코드 복사
    $ git remote remove 이름
    
    ```
    

---

### **주요 팁**

- **Git은 로컬 버전 관리**, **GitHub는 원격 저장소 제공** 역할.
- 충돌 방지를 위해 작업 전 동기화(Pull/Fetch) 권장.
- 팀 협업 시 코드 리뷰와 Pull Request 적극 활용.

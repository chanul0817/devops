# Docker Compose 정리

## 1. Docker Compose란?

Docker Compose는 여러 개의 컨테이너로 구성된 애플리케이션을  
하나의 YAML 파일로 정의하고, 명령 한 줄로 실행할 수 있게 해주는 도구입니다.

즉, **멀티 컨테이너 애플리케이션을 손쉽게 관리**할 수 있습니다.

---

## 2. 주요 특징

- `docker-compose.yml` 파일 하나로 서비스 정의
- 명령 한 줄로 모든 컨테이너 실행 및 종료
- 자동 네트워크 구성
- 볼륨, 환경변수, 포트, 의존성 등 쉽게 설정 가능

---

## 3. 기본 구조 예시

```yaml
version: "3.8"

services:
  web:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - .:/app
  redis:
    image: redis:alpine
```

## 4. 주요 명령어

| 명령어 | 설명 |
|--------|------|
| `docker-compose up` | 컨테이너 시작 |
| `docker-compose up -d` | 백그라운드 실행 |
| `docker-compose down` | 컨테이너 종료 및 네트워크 제거 |
| `docker-compose build` | 이미지 수동 빌드 |
| `docker-compose ps` | 실행 중인 컨테이너 확인 |
| `docker-compose logs -f` | 로그 실시간 확인 |
| `docker-compose exec web sh` | 실행 중인 web 컨테이너 안으로 접속 |
| `docker-compose restart web` | web 서비스만 재시작 |

---

## 5. 사용 예시
1단계: 프로젝트 구조
### my-app/
```
├── app.py
├── Dockerfile
└── docker-compose.yml
```
### Dockerfile
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install flask
CMD ["python", "app.py"]
```
### docker-compose.yml
```yaml
version: "3.8"

services:
  web:
    build: .
    ports:
      - "5000:5000"
```
### 실행
```
docker-compose up
```
## 6. 사용 목적
여러 서비스 컨테이너를 하나의 파일로 관리

개발 및 테스트 환경 구성

로컬에서 간편하게 전체 시스템 실행

## 7. 헷갈렸던 점

- `up -d`는 뒤에서 실행하고 바로 터미널을 돌려준다
- `down`을 하면 compose가 만든 네트워크까지 같이 정리된다
- 로그 볼 때는 `logs -f`를 많이 쓴다
- 컨테이너 안에서 확인할 게 있으면 `exec`로 들어가면 된다

## 8. 개발할 때 자주 하는 식

백엔드, DB, Redis 같이 여러 개 띄워야 할 때 compose를 많이 씀

예를 들어 로컬에서 API 서버 띄우고 DB 붙여볼 때는 이런 식으로 두고 시작하면 편하다

```yaml
services:
  app:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      - db
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://db:3306/app

  db:
    image: mysql:8
    environment:
      MYSQL_DATABASE: app
      MYSQL_ROOT_PASSWORD: root
```

- compose 안에서는 `localhost` 대신 서비스명으로 붙는 경우가 많음
- 그래서 위 예시도 DB 주소를 `db:3306`으로 적음

## 9. 한번씩 막히는 부분

- `docker compose up` 했는데 앱이 바로 죽으면 앱 로그보다 먼저 환경변수 이름이 맞는지 보는 게 빠를 때가 많음
- `depends_on`을 걸어도 DB가 "실행"만 된 상태일 수 있어서, 앱 쪽에서 재시도 로직이 없으면 처음 기동 때 실패하기도 함
- 포트 충돌 나면 이미 로컬에서 같은 포트를 쓰는 프로세스가 있는지 같이 확인해야 함


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

## 10. 요즘 명령어 쓸 때 헷갈린 점

- 예전 자료에는 `docker-compose`가 많이 나오는데, 요즘은 `docker compose`로 치는 경우가 더 많음
- 둘 다 비슷하게 보이지만 환경에 따라 `docker-compose`만 없고 `docker compose`만 되는 경우도 있어서 명령어부터 확인하는 게 덜 헷갈림

```bash
docker compose version
docker-compose version
```

- 팀 문서에 둘 중 하나로 통일해두면 처음 들어온 사람이 덜 헤맨다

## 11. 컨테이너만 다시 띄운다고 끝나지 않을 때

코드만 바뀐 게 아니라 이미지 빌드 내용이 바뀌었으면 `restart`보다 다시 빌드해서 올리는 쪽이 맞다.

```bash
docker compose up -d --build
```

- Dockerfile 수정했는데 반영이 안 되면 `restart`만 한 경우가 많음
- 환경변수 값이 바뀐 경우도 `up -d`로 다시 적용해보는 편이 빠름

볼륨 때문에 예전 데이터가 남아서 이상하게 보일 때도 있다.

```bash
docker compose down -v
```

- DB 테스트하다가 스키마가 꼬였을 때 종종 이걸로 다시 시작함
- 대신 named volume까지 지워지니까 로컬 데이터 날려도 되는 상황에서만 써야 함

### env 파일 쓰기

```yaml
services:
  app:
    env_file:
      - .env
```

- DB 주소, 포트, 이미지 태그처럼 바뀌는 값은 `.env`로 빼두면 편함
- 단, 비밀번호 파일을 Git에 올리지 않도록 `.gitignore`도 같이 확인

### depends_on을 믿을 때 조심

- `depends_on`은 컨테이너 시작 순서를 맞추는 용도에 가까움
- DB가 실제로 접속 가능한 상태가 됐는지는 별도로 확인해야 함
- 앱에서 retry를 넣거나 healthcheck와 같이 보는 편이 좋음

### compose 로그 확인

```bash
docker compose logs -f app
```

- 여러 컨테이너를 같이 띄울 때는 서비스 이름별로 로그를 나눠 보는 게 편함
- `--tail`로 최근 로그만 보면 긴 로그에서 덜 헤맴
- 재시작이 반복되면 `ps`로 상태도 같이 확인


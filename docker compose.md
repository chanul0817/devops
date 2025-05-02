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
'''

## 4. 주요 명령어

| 명령어 | 설명 |
|--------|------|
| `docker-compose up` | 컨테이너 시작 |
| `docker-compose up -d` | 백그라운드 실행 |
| `docker-compose down` | 컨테이너 종료 및 네트워크 제거 |
| `docker-compose build` | 이미지 수동 빌드 |
| `docker-compose ps` | 실행 중인 컨테이너 확인 |

---

## 5. 사용 예시
1단계: 프로젝트 구조
'''my-app/
├── app.py
├── Dockerfile
└── docker-compose.yml
Dockerfile
'''
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install flask
CMD ["python", "app.py"]
docker-compose.yml
'''
version: "3.8"

services:
  web:
    build: .
    ports:
      - "5000:5000"
실행
'''
docker-compose up
'''
## 6. 사용 목적
여러 서비스 컨테이너를 하나의 파일로 관리

개발 및 테스트 환경 구성

로컬에서 간편하게 전체 시스템 실행




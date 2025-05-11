# Ubuntu Repository 정리
## 리포지토리란?
- **패키지를 저장하고 배포하는 서버 또는 저장소**
- 우분투 시스템에서 `apt` 명령어로 설치하는 모든 소프트웨어는 이 리포지토리에서 가져옴
- 보안 업데이트, 새로운 버전 제공 등도 리포지토리를 통해 이루어짐
---
## 기본 리포지토리 구성
우분투 리포지토리는 크게 다음 4가지 섹션으로 나뉨:
| 구분 | 설명 |
|--------------|------|
| **Main** | 우분투에서 공식 지원하는 오픈소스 소프트웨어 |
| **Universe** | 커뮤니티가 관리하는 오픈소스 소프트웨어 |
| **Restricted** | 오픈소스는 아니지만 제한적으로 지원하는 드라이버 등 |
| **Multiverse** | 라이선스 제한이 있는 소프트웨어 (코덱, 일부 게임 등) |
---
## 리포지토리 종류
| 종류 | 설명 |
|--------------------|------|
| **Official (공식)** | Canonical이 제공하는 우분투 기본 리포지토리 |
| **PPA** (Personal Package Archive) | 개인이나 단체가 Launchpad에 등록한 리포지토리 (예: 최신 버전 프로그램 사용 시) |
| **Third-party** | 외부 소프트웨어 제공자가 운영 (예: Docker, VS Code 등 공식 제공 리포지토리) |
---
## 리포지토리 설정 파일
- 위치: `/etc/apt/sources.list`
- 디렉토리: `/etc/apt/sources.list.d/`
- 형식:
```bash
deb http://archive.ubuntu.com/ubuntu focal main universe
```
- `deb`: 바이너리 패키지
- `deb-src`: 소스 패키지
---
## 주요 명령어
```bash
sudo apt update # 리포지토리에서 최신 패키지 목록 받아오기
sudo apt upgrade # 설치된 패키지 최신 버전으로 업그레이드
sudo apt install package-name # 패키지 설치
sudo add-apt-repository ppa:xxx/yyy # PPA 추가
```
---
## 참고
- **한국 미러 서버**: 속도 향상을 위해 한국에 있는 미러 서버를 사용할 수 있음
```bash
http://kr.archive.ubuntu.com/ubuntu
```
- **PPA 사용 시 주의**: 신뢰할 수 있는 소스인지 확인 필요. 보안 이슈 발생 가능성 있음
---
## TIP
> 우분투에서 소프트웨어를 설치할 때 직접 소스코드를 빌드하지 않고, `apt`를 통해 편리하게 설치할 수 있는 이유가 바로 리포지토리 덕분입니다!

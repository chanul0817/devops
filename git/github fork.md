# GitHub Fork 정리
---
### Fork가 뭐야?
- 다른 사람의 GitHub 저장소를 **내 계정으로 복사**하는 기능
- 오픈소스 프로젝트에 기여하고 싶을 때 주로 사용함
---
### 사용할때
- 내가 마음대로 못 고치는 다른 사람의 저장소를 수정하고 싶을 때
- pull request 보내기 전에 내 계정에서 먼저 작업하려고
---
### 흐름 정리
1. 다른 사람 저장소 → `Fork` 클릭
2. 내 계정으로 똑같은 저장소 복사됨
3. 복사된 저장소에서 브랜치 만들고 수정함
4. 수정 다 하면 원본 저장소에 `pull request` 보냄
---
### Fork vs Clone
| 구분 | Fork | Clone |
|------|------|-------|
| 저장 위치 | 내 GitHub 계정 | 내 컴퓨터 |
| 용도 | 웹상에서 내 계정에 복사 | 로컬 작업용 |
| 관계 | 원본 저장소와 연결됨 | 독립적임 |
---
### 주의할 점
- 원본 저장소와 분리돼서 동기화가 안 됨 → **upstream 설정** 필요함
- 너무 오래된 fork는 pull 받아서 업데이트 해줘야 함
```bash
git remote add upstream https://github.com/원본/저장소.git
git fetch upstream
git merge upstream/main
```
---
### 요약
- fork는 남의 저장소를 내 GitHub로 복사하는 것
- 오픈소스 수정하고 PR 보내는 데 자주 씀
- clone은 내 로컬로 가져오는 거고 fork는 GitHub 계정 기준 복사임

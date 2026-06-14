# AWS S3 버킷 및 파일 관련 CLI 명령어 정리

## 버킷 관리

- 버킷 생성
  aws s3 mb s3://버킷명

- 버킷 목록 조회
  aws s3 ls

- 버킷 제거 (내용물이 있으면 --force 필요)
  aws s3 rb s3://버킷명 --force

## 파일 업로드/다운로드 및 목록 조회

- 버킷 내 파일 목록 조회
  aws s3 ls s3://버킷명

- 로컬 파일 S3로 업로드
  aws s3 cp 파일명 s3://버킷명/경로/

- S3 파일 로컬로 다운로드
  aws s3 cp s3://버킷명/파일명 ./로컬경로

## 삭제 및 디렉토리 업로드

- S3 내 파일 삭제
  aws s3 rm s3://버킷명/파일명

- 디렉토리 전체 업로드 (하위 디렉토리 포함)
  aws s3 cp . s3://버킷명 --recursive

- S3 전체 파일 삭제
  aws s3 rm s3://버킷명 --recursive

## 동기화

- 로컬 → S3 동기화
  aws s3 sync ./ s3://버킷명

- S3 → 로컬 동기화
  aws s3 sync s3://버킷명 ./

- 로컬에서 지운 파일도 S3에서 같이 지우기
  aws s3 sync ./ s3://버킷명 --delete

- 실제 반영 전에 먼저 확인
  aws s3 sync ./ s3://버킷명 --dryrun

`sync --delete`는 편하지만 잘못 쓰면 S3 파일이 같이 지워질 수 있음.
처음에는 `--dryrun`으로 어떤 파일이 올라가고 지워지는지 먼저 보는 게 안전함.

### 자주 쓰는 상황

- React나 Vue 빌드 결과물을 정적 호스팅 버킷에 올릴 때
  aws s3 sync ./dist s3://버킷명 --delete

- EC2에서 백업 파일을 S3로 올릴 때
  aws s3 sync /home/ubuntu/backup s3://버킷명/backup

- 로그나 백업 폴더는 실수로 다 지워질 수 있으니 `--delete`를 바로 붙이기보다 먼저 `--dryrun`으로 확인하는 편이 안전함

- AWS 계정을 여러 개 쓰면 프로필까지 같이 명시해두는 편이 덜 헷갈림
  aws s3 sync ./dist s3://버킷명 --delete --profile prod

## 특정 파일만 업로드 또는 제외

- 특정 확장자 제외하고 업로드
  aws s3 cp . s3://버킷명 --recursive --exclude "*.log" --include "*.txt"

- 삭제 테스트(dryrun)
  aws s3 rm s3://버킷명/폴더명 --dryrun

`--exclude`랑 `--include`는 뒤에 나온 규칙이 다시 덮는 식이라 순서를 헷갈리면 원하는 파일이 안 올라갈 수 있음.  
보통은 `--exclude "*"`로 전체를 한번 막고 `--include "*.css"`처럼 필요한 것만 다시 여는 식으로 많이 씀.

## 퍼블릭 권한 부여

- public-read 권한 부여
  aws s3 cp 파일명 s3://버킷명 --acl public-read

- grants 옵션 사용
  aws s3 cp 파일명 s3://버킷명 --grants read=uri=http://acs.amazonaws.com/groups/global/AllUsers

버킷 정책이나 퍼블릭 액세스 차단이 걸려 있으면 `--acl public-read`만 붙여도 바로 공개되지 않을 수 있음.
정적 웹 호스팅이나 이미지 링크 테스트할 때는 CLI 명령어만 보지 말고 버킷 설정도 같이 확인해야 함.

### 자주 만나는 에러

- `An error occurred (AccessDenied)`가 뜨면
  버킷 정책, IAM 권한, KMS 암호화 키 권한 중 어디서 막히는지 같이 봐야 함

- `NoSuchBucket`가 뜨면
  버킷 이름 오타도 많지만, 다른 리전에 만든 버킷인데 잘못된 프로필로 보는 경우도 자주 있음

- EC2에서 업로드할 때는
  인스턴스에 붙은 IAM Role이 있는지 먼저 보고, 로컬에서 쓰던 `~/.aws/credentials` 기준으로 생각하면 자주 꼬임

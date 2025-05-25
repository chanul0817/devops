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

## 특정 파일만 업로드 또는 제외

- 특정 확장자 제외하고 업로드
  aws s3 cp . s3://버킷명 --recursive --exclude "*.log" --include "*.txt"

- 삭제 테스트(dryrun)
  aws s3 rm s3://버킷명/폴더명 --dryrun

## 퍼블릭 권한 부여

- public-read 권한 부여
  aws s3 cp 파일명 s3://버킷명 --acl public-read

- grants 옵션 사용
  aws s3 cp 파일명 s3://버킷명 --grants read=uri=http://acs.amazonaws.com/groups/global/AllUsers

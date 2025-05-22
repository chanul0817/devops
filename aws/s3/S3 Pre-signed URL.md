# S3 Pre-signed URL 정리
## Pre-signed URL이란?
- S3 객체에 임시로 접근할 수 있는 URL
- 일정 시간이 지나면 만료됨
- GET, PUT 등 메서드 설정 가능
- 파일 다운로드/업로드 모두 가능
---
## S3 접근 방식 비교
1. **모든 파일 퍼블릭 설정**
- 장점: 관리 간편
- 단점: 누구나 접근 (보안 취약)
2. **IAM 자격증명 공유 (Access Key Pair)**
- 장점: 특정인만 공유 가능
- 단점: 유출 시 전체 영향, 관리 어려움
3. **IAM 사용자(Role) 부여**
- 장점: 지정 사용자에게만 권한
- 단점: IAM 사용자 수 제한, 유지보수 번거로움
---
## Pre-signed URL 장점
- 버킷을 퍼블릭으로 열지 않아도 됨
- 사용자에게 권한 직접 부여할 필요 없음
- 만료 시간으로 보안 유지
---
## 🖱 웹 콘솔에서 발급 방법
- S3 콘솔 → 객체 선택 → "Pre-signed URL 생성"
---
## AWS CLI에서 발급 방법
### 기본 명령어
```bash
aws s3 presign s3://버킷명/파일명 --region 리전명
```
### 만료 시간 설정 (예: 15초)
```bash
aws s3 presign s3://버킷명/파일명 --region 리전명 --expires-in 15
```
### 주소 스타일 설정 (버추얼 방식)
```bash
aws configure set default.s3.addressing_style virtual
```
---
## EC2를 이용한 발급 예시
1. S3 접근 권한이 없는 사용자가 EC2에 요청
2. EC2는 IAM Role을 통해 S3에 pre-signed URL 요청
3. S3가 URL을 발급해서 EC2에 전달
4. EC2가 사용자에게 전달
5. 사용자는 URL로 다운로드/업로드 수행

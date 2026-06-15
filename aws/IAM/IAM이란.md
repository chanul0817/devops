# AWS IAM 핵심 요약
## 1. IAM이란?
- AWS 리소스 접근을 제어하는 서비스
- 누가(Who), 무엇을(What), 어디서(Where), 어떻게(How) 할 수 있는지 설정
## 2. 주요 구성요소
| 개념 | 설명 |
|--------|-------------------------------------------|
| User | 개별 사용자 또는 서비스 계정 |
| Group | 사용자 집합, 권한 일괄 적용 |
| Role | 임시 권한 부여, 서비스/외부에 권한 위임 |
| Policy | JSON 형식의 권한 문서 (허용/거부) |
## 3. 실전 활용 예시
- EC2에서 S3 접근: EC2에 Role 할당
- 개발자별 접근 제어: User 생성 후 Group으로 관리
- 외부 서비스 접근: Role + Policy 연결 후 제공
- MFA 적용: 사용자별 2단계 인증

## 3-1. User와 Role 감각
- 사람이 직접 콘솔에 로그인하거나 CLI를 쓸 때는 보통 User를 생각함
- EC2, Lambda 같은 AWS 서비스가 다른 AWS 리소스에 접근할 때는 Role을 붙이는 게 자연스러움
- Access Key를 서버에 직접 넣는 것보다 Role을 쓰는 편이 안전함
- Role은 필요한 순간에 임시 자격 증명을 받아 쓰는 방식이라 키 노출 위험이 줄어듦

## 4. 자주 쓰는 정책
- AmazonS3ReadOnlyAccess: S3 읽기만 허용
- AmazonEC2FullAccess: EC2 전체 권한
- AdministratorAccess: 전체 권한 (주의)
## 5. IAM 보안 팁
- 루트 계정 사용 금지
- 최소 권한 원칙 적용
- Access Key 노출 주의 (절대 Git에 올리지 말기)
- MFA 필수 설정
- 정책 시뮬레이터, CloudTrail, Access Analyzer 활용
## 6. 한눈에 비교
| 개념 | 설명 |
|--------|----------------------------|
| User | 개별 사용자 |
| Group | 사용자 집합 |
| Role | 임시 권한, 위임 |
| Policy | 권한 문서 |

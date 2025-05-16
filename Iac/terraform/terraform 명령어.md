# Terraform 명령어 정리

## 1. 초기화
```bash
terraform init
```
- Terraform 초기화
- provider 다운로드, .terraform 폴더 생성

## 2. 실행 계획 확인
```bash
terraform plan
```
- 적용 전 시뮬레이션
- 어떤 리소스가 생성/변경/삭제될지 보여줌

## 3. 실제 적용
```bash
terraform apply
```
- 실제로 리소스를 배포함
- 자동 승인 옵션: `-auto-approve`

## 4. 리소스 삭제
```bash
terraform destroy
```
- 모든 리소스를 삭제
- 자동 승인 옵션: `-auto-approve`

## 5. 상태 확인
```bash
terraform show
```
- 현재 상태 파일(tfstate)에 저장된 정보 출력

```bash
terraform state list
```
- 관리 중인 리소스 목록 출력

## 6. 문법 검사 & 포맷팅
```bash
terraform validate
```
- 문법 검사

```bash
terraform fmt
```
- 코드 자동 정리 (형식 맞춤)

## 7. output 값 출력
```bash
terraform output
```
- output 블록에 정의된 변수 출력

## 8. 모듈 다운로드
```bash
terraform get
```
- 필요한 모듈들 다운로드

## 9. 백엔드 재설정
```bash
terraform init -reconfigure
```
- 백엔드 설정 바뀐 경우 재초기화

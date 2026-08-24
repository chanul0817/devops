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

### apply 전에 plan 보기

```bash
terraform plan
terraform apply
```

- `plan`은 실제로 무엇이 생성/수정/삭제될지 미리 보여줌
- 운영 환경에서는 `apply` 전에 `plan` 결과를 꼭 확인하는 게 안전함

### drift 확인

- 콘솔에서 직접 바꾼 리소스는 코드와 실제 상태가 벌어질 수 있음
- `terraform plan`에서 의도하지 않은 변경이 보이면 먼저 원인을 확인
- 운영에서는 직접 수정보다 코드 변경으로 맞추는 편이 좋음

### output 쓰는 이유

- 생성된 리소스 주소나 이름을 다음 단계에서 확인하기 쉽게 남김
- 민감한 값은 output으로 그대로 노출하지 않도록 조심
- 모듈 간 연결 값을 정리할 때도 자주 사용함

### terraform 작업 - 처음 볼 때

- 콘솔에서 직접 바꾼 값은 drift로 잡힐 수 있음
- terraform 문제는 처음부터 결론내기보다 증상을 먼저 작게 나누는 게 좋음
- provider 버전과 lock 파일 변경도 같이 확인

### terraform 작업 - 배포 직후

- apply 전에는 plan에서 삭제 항목을 먼저 확인
- 배포 직후 terraform 쪽 지표가 흔들리면 변경 범위부터 다시 확인
- 운영 IaC는 작은 변경으로 나눠서 보는 편이 안전함

### terraform 작업 - 장애 시간대

- state가 실제 리소스와 맞는지 의심될 때가 있음
- terraform 확인은 장애가 시작된 시간과 마지막 정상 시간을 같이 잡고 봄
- 민감한 값이 plan이나 output에 노출되지 않게 조심

### terraform 작업 - 권한 볼 때

- 콘솔에서 직접 바꾼 값은 drift로 잡힐 수 있음
- terraform 문제가 권한처럼 보여도 경로, 대상, 실행 주체를 같이 확인
- provider 버전과 lock 파일 변경도 같이 확인


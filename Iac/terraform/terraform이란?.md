## Terraform 정리

---

### Terraform이 뭐야?

- **IaC 도구** (Infrastructure as Code)
- 클라우드 자원을 코드로 정의하고 배포하게 해주는 툴
- AWS, Azure, GCP 등 여러 클라우드 지원
- 설정은 `.tf` 파일로 작성하고, HCL (HashiCorp Configuration Language) 사용함

---

### 주요 특징

- 선언형 방식 (원하는 상태만 적으면 됨)
- 상태파일(`terraform.tfstate`)로 현재 인프라 상태 관리함
- 여러 클라우드 플랫폼을 하나의 도구로 관리 가능

---

### 기본 구조

보통 이런 구성으로 되어 있음:

```hcl
provider "aws" {
  region = "ap-northeast-2"
}

resource "aws_instance" "example" {
  ami           = "ami-0abcd1234"
  instance_type = "t2.micro"
}
```

---

### Terraform 기본 명령어

| 명령어                  | 설명 |
|-------------------------|------|
| `terraform init`        | 초기화 (모듈, 플러그인 설치 등) |
| `terraform plan`        | 무슨 일이 일어날지 미리 확인 (변경 사항 시뮬레이션) |
| `terraform apply`       | 실제 인프라 생성 또는 수정 |
| `terraform destroy`     | 만든 리소스 전부 삭제 |
| `terraform fmt`         | `.tf` 파일 포맷 정리 |
| `terraform validate`    | 코드 문법 오류 체크 |
| `terraform show`        | 현재 상태파일에 기록된 리소스 보기 |
| `terraform output`      | output 변수 값 출력 |
| `terraform state list`  | 관리 중인 리소스 목록 보기 |
| `terraform taint 리소스` | 특정 리소스를 강제로 다시 생성 처리 |

---

### 예시 흐름

```bash
terraform init
terraform plan
terraform apply
```

→ 인프라 생성

```bash
terraform destroy
```

→ 리소스 전부 삭제

---

### 정리 요약

- Terraform은 클라우드 자원을 코드로 관리하게 해주는 도구
- `.tf` 파일에 작성하고 명령어로 배포하거나 삭제 가능
- `init`, `plan`, `apply`, `destroy` 자주 씀

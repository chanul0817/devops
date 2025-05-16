# HCL (HashiCorp Configuration Language)

## 개요
- HashiCorp에서 만든 **구성 언어(Configuration Language)**
- Terraform, Packer 등에서 사용됨
- **사람이 읽기 쉽고**, **머신이 파싱하기 쉬움**
- `.tf`, `.tfvars` 파일에서 사용됨

---

## 특징
- JSON과 유사하지만 **더 직관적**이고 **가독성 좋음**
- 중괄호 `{}`와 키-값 구조 사용
- 변수, 조건문, 반복문 지원
- `Terraform`의 인프라 정의에 최적화됨

---

## 기본 문법 예시

```hcl
provider "aws" {
  region = "ap-northeast-2"
}

resource "aws_instance" "example" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t2.micro"
}
```

- `provider`: 어떤 클라우드를 쓸지 정의
- `resource`: 만들 리소스를 정의 (여기선 EC2 인스턴스)

---

## 데이터 타입
```hcl
string  = "hello"
number  = 42
bool    = true
list    = ["a", "b", "c"]
map     = {
  name = "sungwoo"
  age  = 30
}
```

---

## 조건문 & 반복문

```hcl
# 조건문
variable "env" {
  default = "dev"
}

output "instance_count" {
  value = var.env == "prod" ? 3 : 1
}

# 반복문
output "names" {
  value = [for name in ["a", "b"] : upper(name)]
}
```

---

## JSON도 사용 가능
Terraform은 `.json` 확장자로 된 JSON 형식도 지원하지만, **직접 작성할 땐 대부분 HCL을 사용**함.  
가독성, 유지보수 측면에서 훨씬 편함.

---

## 요약
- Terraform 설정 파일에 쓰이는 **DSL(도메인 특화 언어)**
- 사람 친화적인 문법
- 조건문, 반복문 등도 지원
- `.tf` 파일은 사실상 HCL 파일

# IAM Policy JSON 구조 요약

## 1. 기본 구조 예시

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "FirstStatement",
      "Effect": "Allow",
      "Action": [
        "s3:List*",
        "s3:Get*"
      ],
      "Resource": [
        "arn:aws:s3:::confidential-data",
        "arn:aws:s3:::confidential-data/*"
      ],
      "Condition": {
        "Bool": {"aws:MultiFactorAuthPresent": "true"}
      }
    }
  ]
}
```
---

## 2. 주요 요소 설명

- **Version**: 정책 문서 버전 (2012-10-17 권장)
- **Statement**: 실제 권한 규칙 배열(여러 개 가능)
- **Sid**: Statement 식별자(선택)
- **Effect**: Allow(허용) 또는 Deny(거부)
- **Action/NotAction**: 허용/거부할 AWS 서비스 API(예: s3:GetObject)
- **Resource/NotResource**: 정책이 적용될 리소스(ARN 형식)
- **Principal/NotPrincipal**: 정책 적용 대상(리소스 기반 정책에서 사용)
- **Condition**: 조건부 정책(예: MFA 사용 시만 허용)

---

## 3. Action/Resource 예시
```
"Action": [
  "s3:PutObject",
  "s3:GetObjectAcl",
  "s3:GetObject",
  "s3:ListBucket",
  "s3:DeleteObject",
  "s3:PutObjectAcl"
]

"Resource": [
  "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
  "arn:aws:dynamodb:us-east-2:account-ID-without-hyphens:table/books_table"
]
```
---

## 4. Condition 예시
```
"Condition": {
  "NumericLessThanEquals": {
    "s3:max-keys": "10"
  }
}
```

## 5. 정책 유형

| 유형                | 연결 대상                | Principal 속성 | 관리 위치           |
|---------------------|-------------------------|----------------|--------------------|
| 자격증명 기반 정책  | 사용자, 그룹, 역할 등    | 안 들어감      | IAM Policy 메뉴    |
| 리소스 기반 정책    | S3, SQS 등 리소스       | 반드시 필요    | 리소스 자체(버킷 등)|

---

## 6. ARN(Amazon Resource Name) 형식

- AWS 리소스를 고유하게 식별하는 문자열
- 일반 구조:
  arn:partition:service:region:account-id:resource-id
  arn:partition:service:region:account-id:resource-type/resource-id
  arn:partition:service:region:account-id:resource-type:resource-id

- 예시
  - S3 버킷: arn:aws:s3:::bucket_name
  - S3 오브젝트: arn:aws:s3:::bucket_name/key_name
  - DynamoDB 테이블: arn:aws:dynamodb:us-east-2:account-id:table/table_name
  - IAM 사용자: arn:aws:iam::account-id:user/username

---

## 7. 참고 팁

- 요소 순서는 중요하지 않음
- Action/NotAction, Principal/NotPrincipal, Resource/NotResource는 택일
- Condition은 선택 사항
- 자격증명 기반 정책은 Principal 생략, 리소스 기반 정책은 반드시 명시

# AWS 자격 증명 핵심 정리

## 1. AWS 자격 증명 종류

- **콘솔 액세스 자격 증명**
  - AWS 웹 콘솔 로그인용
  - Root 이메일/비밀번호, IAM 유저명/비밀번호, MFA(다단계 인증) 등

- **프로그램 방식 액세스 자격 증명**
  - CLI, SDK 등 프로그래밍 방식 접근에 사용
  - Access Key ID(아이디 역할, 공개 가능), Secret Access Key(비밀번호 역할, 절대 공개 X), Token(임시 자격 증명용)

---

## 2. 장기 자격 증명 (Long-Term Credential)

- IAM 유저 생성 시 발급되는 Access Key ID와 Secret Access Key
- 유효기간 제한 없음(삭제 전까지 사용 가능)
- 보안상 유출 시 피해가 크므로 하드코딩 금지, 필요 시 비활성화/재발급 가능
- IAM 유저당 2개까지 발급 가능, Secret Access Key는 분실 시 복구 불가(재발급 필요)

---

## 3. 임시 자격 증명 (Temporary Credential)

- 일정 시간(분~몇 시간)만 유효, 만료 후 자동 폐기
- AWS STS(Security Token Service)로 발급
- 필요 시마다 생성, 자동 로테이션 및 관리 용이
- 보안성이 높아 어플리케이션, 외부 사용자, EC2 등 다양한 주체에 권한 부여 시 권장
- 임시 자격 증명은 IAM Role(역할)을 이용해 발급하며, 외부 인증(페이스북, 구글 등)과 연동도 가능

---

## 4. 임시 자격 증명 발급 및 사용 절차

1. **IAM 유저 생성 및 Access Key 발급**
   - AWS CLI에서 `aws configure`로 등록
2. **AssumeRole 정책 부여**
   - IAM 유저에게 Role을 Assume할 권한 부여
3. **IAM Role 생성**
   - 예: S3 전체 권한 Role 등
4. **STS로 임시 자격 증명 발급**
   - `aws sts assume-role --role-arn ... --role-session-name ...`
   - 반환된 AccessKeyId, SecretAccessKey, SessionToken을 환경변수에 등록
5. **임시 자격 증명으로 AWS 리소스 접근**
   - 예: S3 버킷 조회/수정 등

---

## 5. 실무 팁

- 장기 자격 증명은 최대한 사용하지 말고, 임시 자격 증명을 활용할 것
- Access Key 유출 시 즉시 비활성화 및 재발급 필요
- IAM Role과 정책을 활용해 최소 권한 원칙 적용
- 외부 시스템/사용자 연동 시 SSO, Web Identity, SAML 등 연동 지원

### 많이 보는 사용 흐름

- 로컬에서 CLI 테스트할 때는 `aws configure`로 IAM 유저 키를 넣고 시작하는 경우가 많음
- 그런데 EC2, ECS, Lambda에서는 키를 파일에 넣기보다 Role 붙여서 쓰는 쪽이 일반적
- 특히 EC2에 S3 업로드 작업 붙일 때 인스턴스 프로파일 Role을 달아두면 키를 서버에 남기지 않아도 됨

```bash
aws sts get-caller-identity
```

- 위 명령으로 지금 어떤 자격 증명으로 호출 중인지 바로 확인 가능
- 권한 문제 생기면 이 명령부터 쳐보면 현재 IAM 유저인지, AssumeRole 된 세션인지 구분이 빨라짐

### 헷갈렸던 점

- Access Key ID는 공개되어도 된다고 하지만, 보통 Secret과 같이 노출되는 경우가 많아서 둘 다 조심하는 편이 안전함
- `AccessDenied`가 났다고 무조건 키 문제는 아니고, Role 신뢰 정책이나 S3 버킷 정책 때문에 막히는 경우도 많음
- 임시 자격 증명은 `SessionToken`까지 같이 들어가야 해서, 키 2개만 넣으면 동작 안 할 수 있음

---

## 6. 요약

- 콘솔 로그인: Root, IAM 유저, MFA 등
- CLI/SDK: Access Key ID + Secret Access Key(장기/임시)
- 임시 자격 증명은 STS, Role, 외부 인증 등 다양한 방식으로 발급 가능
- 보안상 임시 자격 증명 활용이 권장됨

### profile 나눠 쓰기

```bash
aws sts get-caller-identity --profile dev
```

- 계정을 여러 개 쓰면 profile을 나눠야 실수를 줄일 수 있음
- 작업 전에는 현재 계정과 권한을 먼저 확인
- 운영 계정 명령은 특히 profile 오타를 조심


# ALB Access Log 핵심 정리

## 1. ALB Access Log란?
- Application Load Balancer(ALB)를 경유하는 모든 트래픽 요청/응답을 기록하는 로그
- 각 요청의 시간, 클라이언트/타겟 IP, 응답 코드, 처리 시간 등 상세 정보 포함
- 트래픽 흐름, 오류, 접속 경로 등 서비스 운영에 필수적인 진단 정보 제공

---

## 2. 로그 저장 및 관리

- **저장 위치**: S3 버킷에 5분 단위로 자동 저장
- **로그 파일 구조**: ALB 인스턴스(네트워크 인터페이스)별로 별도 파일 생성, 고트래픽 시 여러 파일 생성 가능
- **타임스탬프**: UTC 기준, KST 등 타임존 변환 필요
- **요금**: 로그 활성화 자체는 무료, S3 저장 비용만 발생

---

## 3. 로그 포맷

- **고정 포맷(변경 불가)**: 공백(스페이스) 구분, 각 필드는 문서화된 순서로 기록됨
- **주요 필드**
  - type: 요청 유형 (http 등)
  - time: 응답 발생 시간(UTC, ISO 8601)
  - elb: 로드밸런서 리소스 ID
  - client:port: 클라이언트 IP/포트
  - target:port: 타겟 IP/포트
  - request_processing_time / target_processing_time / response_processing_time: 처리 시간(초)
  - elb_status_code: 로드밸런서가 반환한 상태 코드
  - target_status_code: 타겟 서버가 반환한 상태 코드
  - received_bytes / sent_bytes: 요청/응답 바이트 수
  - "request": HTTP 요청 라인
  - "user_agent": User-Agent 문자열
  - ssl_cipher / ssl_protocol: SSL 정보(HTTPS 리스너일 경우)
  - 기타: trace_id, domain_name, actions_executed 등

---

## 4. 주요 활용 및 분석

- **트래픽 분석**: 시간대별, IP별, 경로별 접속량 및 트래픽 분포 파악
- **오류 진단**: elb_status_code, target_status_code로 장애/오류 발생 구간 추적
  - 400, 404: 잘못된 요청
  - 460: 클라이언트가 연결 중단
  - 502, 503, 504: 타겟 서버 장애, 과부하, 타임아웃 등
- **Athena 연동**: S3에 저장된 로그를 Athena로 SQL 쿼리 분석 가능. 대용량 로그 분석에 효과적

---

## 5. 실전 설정 절차 요약

1. S3 버킷 생성 및 버킷 정책(ELB, logdelivery 서비스 권한) 적용
2. ALB 콘솔에서 Access Log 활성화 및 S3 경로 지정
3. S3 버킷에서 로그 파일 확인, 필요시 Athena 테이블 생성 후 SQL 분석

---

## 6. 참고 사항

- 로그 포맷은 AWS가 고정 제공, 커스텀 불가
- ALB 헬스체크 트래픽은 로그에 기록되지 않음
- 로그 반영까지 약간의 지연(최대 수분) 발생할 수 있음
- 고트래픽 환경에서는 여러 네트워크 인터페이스별 로그 파일이 생성됨

---

> ALB Access Log는 서비스 트래픽 모니터링, 장애 진단, 보안 감사에 필수적인 데이터 소스입니다. S3와 Athena를 조합하면 대용량 로그도 손쉽게 분석할 수 있습니다.

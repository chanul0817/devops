# SRE(Site Reliability Engineering) 정리
## 1. SRE 개요
- **SRE**는 시스템의 신뢰성을 높이는 엔지니어링 기반의 운영 방식.
- Google에서 시작되었으며, DevOps와 비슷한 목적을 가지지만 운영에 더 초점을 둠.
- 시스템 가용성(Availability)을 중심으로 운영을 자동화하고 최적화함.
## 2. SRE의 미션
**주요 책임**
- 시스템 가용성 보장
- 지연시간(Latency) 및 성능(Performance) 개선
- 효율성(Efficiency) 유지
- 변화(Change) 관리
- 모니터링(Monitoring)
- 비상 대응(Emergency Response)
- 용량 계획(Capacity Planning)
## 3. SRE 접근법
- 엔지니어링 기반 접근법을 활용하여 문제 해결
- 반복적인 자동화, 코드 기반 운영 문화 강조
- 장애 발생 시 근본 원인을 분석하고 재발 방지 조치 수행
## 4. 시스템 튜닝
- 시스템의 가용성을 위해 CPU, Memory, I/O 등의 리소스를 최적화
- 불필요한 병목 요소 제거 및 성능 분석 도구 활용
## 5. 모니터링
- 주요 지표(CPU, Memory, Network 등)를 실시간 수집 및 분석
- 알림(Alert)을 통해 장애를 사전 예방
- SLO(Site Level Objective), SLA(Service Level Agreement), SLI(Service Level Indicator) 개념 활용
## 6. 장애 대응
- 장애 발생 시 자동화된 복구 시스템 및 대응 프로세스를 통해 빠르게 처리
- 사람이 개입하지 않아도 복구 가능한 시스템 지향
## 7. SRE의 목표
- 엔지니어링 기반으로 시스템의 **신뢰성**과 **비즈니스 연속성**을 최대한 보장하는 것
## 8. SRE와 DevOps의 관계
- DevOps는 문화적 접근, SRE는 구체적 실행 방법론
- DevOps의 실천을 위한 실질적인 프레임워크로서 SRE가 자주 활용됨

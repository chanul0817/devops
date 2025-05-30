# AWS 오토 스케일링(Auto Scaling) 핵심 정리

## 1. 오토 스케일링(Auto Scaling) 개념
- 클라우드의 유연성을 극대화하는 핵심 서비스
- CPU, 메모리, 디스크, 네트워크 등 시스템 자원 메트릭을 모니터링하여 EC2 인스턴스 수를 자동으로 조절
- 예기치 못한 부하에도 안정적이고 예측 가능한 성능 유지, 비용 최적화

---

## 2. 스케일링 종류
- **스케일 업(Scale Up)**: 더 큰 인스턴스로 교체 (성능↑, 비용↑, 한계 존재)
- **스케일 아웃(Scale Out)**: 인스턴스 개수 늘리기 (클라우드 환경에 적합, 성능과 비용 비례)
- **스케일 인(Scale In)**: 인스턴스 개수 줄이기

---

## 3. 오토 스케일링의 목표
- 항상 적정 수의 EC2 인스턴스 유지 (최소/최대/원하는 용량 지정)
- 다양한 조건(메트릭, 일정, 수동 등)에 따라 인스턴스 자동 증감
- 가용영역(AZ)에 인스턴스 분산 → 장애 시에도 서비스 유지

---

## 4. 오토 스케일링 구성요소
- **Auto Scaling 그룹**: 논리적으로 인스턴스를 묶어 관리(최소/최대/원하는 용량 지정)
- **시작 템플릿(Launch Template)**: 인스턴스의 AMI, 타입, 키페어, 보안 그룹 등 사전 정의(버전 관리 가능)
- **조정 옵션(Scaling Policy)**: CPU/네트워크 등 메트릭, 예약 스케줄, 수동 등 다양한 조건으로 증감 정책 설정

---

## 5. 오토 스케일링 동작 원리
1. Auto Scaling 그룹이 인스턴스 수를 모니터링
2. 인스턴스 장애/부족 발생 시, 시작 템플릿 기반으로 새 인스턴스 자동 생성
3. 인스턴스 과다 시 자동 종료
4. 로드밸런서(ELB)와 연동 가능 → 트래픽 자동 분산

---

## 6. 실전 구축 절차 요약
1. **시작 템플릿 생성**: AMI, 인스턴스 타입, 키페어, 보안 그룹 등 설정
2. **Auto Scaling 그룹 생성**: 시작 템플릿 선택, VPC/서브넷/가용영역 지정, 최소/최대/원하는 용량 입력
3. **로드밸런서(옵션)**: 트래픽 분산 필요 시 연결
4. **조정 정책 설정**: 메트릭/스케줄 기반 정책 지정
5. **모니터링 및 관리**: 인스턴스 상태, 용량 자동 관리

---

## 7. 고급 기능 및 활용
- **웜 풀(Warm Pool)**: 미리 준비된 인스턴스를 대기시켜 확장 지연 최소화(비용 추가)
- **예약 작업(Scheduled Action)**: 특정 시간대에 인스턴스 수 미리 조정(예: 주말 트래픽 대응)
- **Predictive Scaling**: 과거 메트릭을 기반으로 미래 수요 예측, 미리 인스턴스 조정

---

## 8. 관리 팁
- 인스턴스 삭제 시 그룹의 최소/원하는/최대 용량을 0으로 설정해야 완전히 종료됨
- 시작 템플릿 수정은 새 버전을 생성해 업데이트
- 태그 활용 시 관리 편리(Name 등)

---

## 9. 주요 연동 서비스
- **CloudWatch**: 모니터링 및 자동 조정 트리거
- **ELB**: 인스턴스 트래픽 분산 및 상태 체크

---

## 10. 요약
- 오토 스케일링은 클라우드의 핵심 자동화 기능
- 적정 인스턴스 수 유지, 장애 자동 복구, 비용 최적화, 트래픽 변화 대응에 필수

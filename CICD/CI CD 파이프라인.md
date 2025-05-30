# CI/CD 파이프라인

### **1. CI/CD란?**

**CI/CD**는 **지속적 통합(Continuous Integration)**과 **지속적 제공/배포(Continuous Delivery/Deployment)**를 의미합니다.

소프트웨어 개발에서 코드 작성부터 배포까지의 모든 과정을 자동화해 개발 속도와 품질을 개선하는 데 목적이 있습니다.

### **CI (Continuous Integration)**

- **정의**: 코드 변경 사항을 자동으로 빌드하고 테스트해 공유 저장소에 병합하는 과정.
- **핵심**:
    - 작은 단위의 코드를 자주 병합.
    - 자동화된 빌드와 테스트.
- **장점**:
    - 충돌 방지.
    - 빠른 문제 발견 및 해결.
    - 코드 품질 향상.

### **CD (Continuous Delivery/Deployment)**

- **정의**: CI를 통해 빌드 및 테스트가 완료된 애플리케이션을 프로덕션 환경에 배포하는 과정.
    - **Delivery**: 수동 배포.
    - **Deployment**: 자동 배포.
- **장점**:
    - 배포 자동화로 시간 절약 및 휴먼 에러 방지.
    - 빠르고 안정적인 배포 가능.

---

### **2. CI/CD 파이프라인**

**CI/CD 파이프라인**은 코드 변경부터 최종 배포까지의 과정을 단계별로 자동화하는 일련의 절차를 의미합니다.

### **주요 단계**

1. **Source**: 코드 변경을 감지.
2. **Build**: 코드를 컴파일, 빌드 및 테스트.
3. **Test**: 호환성과 오류를 검사.
4. **Release**: 애플리케이션을 준비된 상태로 업데이트.
5. **Deploy**: 프로덕션 환경에 배포.

### **파이프라인 장점**

- 배포 시간 단축.
- 문제 발생 시 빠른 롤백.
- 비용 효율적이며, 고객 만족도 증가.

---

### **3. CI/CD 도구 비교**

### **1) Jenkins**

- **장점**: 무료, 다양한 플러그인 지원, 커뮤니티 활성.
- **단점**: 설정 및 운영 복잡, 별도 서버 필요.

### **2) Travis CI**

- **장점**: GitHub 연동 간편, 별도 서버 불필요, 초기 설정 용이.
- **단점**: 로컬 테스트 어려움, 기업용 요금 비쌈.

### **3) GitHub Actions**

- **장점**: GitHub과 완벽 통합, 빠른 속도, 다양한 언어/프레임워크 지원.
- **단점**: 문서 부족, 워크플로우 재실행 제한.

---

### **4. CI/CD 도입 효과**

- 변경 사항의 신속한 감지 및 처리.
- 휴먼 에러 방지.
- 릴리스 주기 단축 및 높은 코드 품질.
- 비즈니스 경쟁력 강화.

## **CI/CD란?**

### **1. Continuous Integration (CI): 지속적 통합**

새로운 코드 변경 사항을 정기적으로 **자동 빌드 및 테스트하여 공유 저장소(Repository)에 통합**하는 프로세스입니다.

- **주요 목표**
    - 팀원 간의 코드 충돌 방지
    - 코드 품질 개선
    - 빠른 피드백 제공
- **CI의 특징**
    - 개발자들은 작은 단위로 코드를 자주 커밋하고 머지해야 합니다.
    - 모든 빌드와 테스트 과정이 자동으로 이루어집니다.
- **CI의 장점**
    1. **충돌 예방:** 작은 단위로 빈번히 통합되므로 충돌 가능성을 줄임.
    2. **빠른 문제 탐지:** 코드 결함을 신속히 발견하고 수정 가능.
    3. **품질 향상:** 테스트 자동화를 통해 안정성 증가.

---

### **2. Continuous Delivery (CD): 지속적 제공**

CI를 통해 빌드와 테스트가 완료된 애플리케이션을 **배포 가능한 상태로 만드는 것**입니다.

- **주요 특징**
    - 코드는 항상 배포 가능한 상태를 유지합니다.
    - 배포는 수동으로 이루어질 수 있습니다.
    - 운영팀과의 협업을 용이하게 합니다.
- **Continuous Delivery의 목적**
    - **최소 노력 배포:** 수동 푸시 작업 없이 배포 가능.
    - **비즈니스 팀과의 협업 향상:** 애플리케이션 상태를 투명하게 공유.

---

### **3. Continuous Deployment (CD): 지속적 배포**

Continuous Delivery를 한 단계 발전시킨 방식으로, **코드가 자동으로 프로덕션 환경에 배포**됩니다.

- **Continuous Deployment의 장점**
    1. **속도 향상:** 배포가 자동화되어 즉각적인 배포 가능.
    2. **운영 부담 감소:** 수동 배포의 프로세스 과부하 제거.
    3. **고객 만족도 향상:** 새로운 기능이나 버그 수정이 빠르게 적용 가능.

---

## **CI/CD 파이프라인**

### CI/CD 파이프라인이란?

소스 코드가 빌드, 테스트, 릴리즈, 배포까지 **자동화된 단계**를 거쳐 처리되는 흐름입니다.

- *"파이프라인"**이라는 표현은 단계별 처리가 물 흐르듯 진행되는 것을 나타냅니다.

---

### **CI/CD 파이프라인의 구성 요소**

1. **소스(Source):**
    - 변경 사항이 저장소에 커밋되면, 파이프라인이 이를 감지합니다.
2. **빌드(Build):**
    - 코드를 컴파일하고 애플리케이션을 생성.
    - 필요한 라이브러리 및 의존성 설치.
3. **테스트(Test):**
    - 유닛 테스트, 통합 테스트, 성능 테스트를 통해 품질 검증.
4. **릴리즈(Release):**
    - 테스트를 통과한 빌드 결과물을 배포 가능한 상태로 준비.
5. **배포(Deploy):**
    - 결과물을 프로덕션 환경에 배포.
    - 롤백 가능성을 포함한 안전한 배포 수행.

---

### **파이프라인 단계의 상세 작업**

1. **Source 단계**
    - 소스 코드 저장소(예: GitHub)에서 변경 사항을 감지.
    - 변경 내용이 있을 때 다음 단계로 진행.
2. **Build 단계**
    - 코드 빌드 및 컴파일.
    - 실행 파일 또는 배포 가능한 아티팩트 생성.
3. **Test 단계**
    - 자동화된 테스트 실행.
    - 유닛 테스트, 통합 테스트, 성능 테스트 등 포함.
4. **Deploy 단계**
    - 스테이징 환경에 배포.
    - 프로덕션 환경으로 최종 배포.
5. **Monitor 및 Notification 단계 (선택 사항)**
    - 배포 상태를 모니터링.
    - 알림을 통해 배포 성공 여부를 팀에 공유.

---

### **CI/CD의 도구들**

다양한 도구가 CI/CD 파이프라인 구축을 지원합니다.

1. **Jenkins**
    - **장점**: 무료, 플러그인 다양, 문서와 커뮤니티 풍부.
    - **단점**: 별도 서버 필요, 설정이 복잡.
2. **Travis CI**
    - **장점**: GitHub 연동, 초기 설정이 간편.
    - **단점**: 플러그인이 적고, 비용이 높음(기업용).
3. **GitHub Actions**
    - **장점**: GitHub 통합, 빠르고 간편함.
    - **단점**: 문서 부족, 일부 기능 제한.
4. **AWS CodePipeline**
    - **장점**: AWS 서비스와의 높은 호환성.
    - **단점**: AWS 외 서비스 사용 시 복잡.

---

### **CI/CD 도입의 효과**

1. **효율성 증가**: 자동화로 반복적인 수작업 제거.
2. **휴먼 에러 감소**: 배포 과정을 표준화하여 실수 방지.
3. **속도 향상**: 빠른 릴리즈 사이클로 신규 기능 제공.
4. **비용 절감**: 운영 및 개발 시간을 줄임.
5. **품질 향상**: 테스트 자동화를 통한 안정성 증가.

---

### **실제 활용 사례**

- **배포 자동화:** 코드 변경 시 자동으로 빌드, 테스트, 배포가 이루어짐.
- **롤백 기능:** 배포 문제 발생 시 이전 안정 버전으로 쉽게 복구.
- **알림 시스템:** Slack, 이메일 등을 통한 배포 상태 알림.

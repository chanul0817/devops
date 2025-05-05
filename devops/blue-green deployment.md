# Blue-Green Deployment
## 개요
- **Blue-Green Deployment**는 두 개의 거의 동일한 프로덕션 환경(Blue, Green)을 번갈아 가며 사용하는 배포 전략이다.
- 사용자 트래픽을 기존 환경(예: Blue)에서 새로운 환경(예: Green)으로 전환함으로써 다운타임 없이 안전한 배포를 실현할 수 있다.
## 장점
1. **릴리스로 인한 문제를 빠르게 롤백 가능**
- 문제가 발생하면 트래픽을 기존 환경(Blue)으로 즉시 전환하여 빠르게 대응 가능.
2. **테스트 환경과 운영 환경이 동일**
- 배포 전 Green 환경에서 충분히 테스트 가능.
3. **무중단 배포 가능**
- 트래픽을 전환하는 것만으로 배포 완료되므로 사용자는 영향을 받지 않음.
4. **점진적 전환도 가능**
- Cookie 또는 Load balancer 설정 등을 통해 일부 사용자만 Green으로 우선 전환하는 A/B 테스트와 같은 전략도 가능.
## 구현 방식
### 1. Load Balancer를 이용한 방식
- Load balancer가 Blue 환경 또는 Green 환경 중 하나를 선택하여 트래픽을 전달.
- Blue에서 Green으로, 혹은 반대로 전환할 때는 Load balancer 설정만 변경하면 됨.
### 2. DNS를 이용한 방식
- DNS의 CNAME을 조작해 `example.com` 도메인을 `blue.example.com` 또는 `green.example.com`으로 연결.
- 단점: DNS TTL로 인해 전환이 지연될 수 있음.
### 3. Cookie를 이용한 방식
- 사용자에게 발급된 Cookie 값을 기준으로 트래픽을 Blue 또는 Green으로 라우팅.
- A/B 테스트나 점진적 전환 등에 유용.
## 주의점
- **Active-Standby 구조**와 **Active-Active 구조**의 혼동을 피해야 함.
- Blue-Green은 일반적으로 한 쪽만 활성화된 Active-Standby 구조를 의미함.
- 무분별한 트래픽 전환은 문제를 유발할 수 있으므로 사전 테스트 및 계획이 중요.
## 결론
- **Immutable Infrastructure**와 매우 잘 맞는 배포 전략.
- 다운타임을 최소화하고, 배포 위험을 줄이며, 빠른 롤백을 가능하게 하는 안전한 방식이다.

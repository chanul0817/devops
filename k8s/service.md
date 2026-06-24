# Kubernetes Service 정리

## Service가 필요한 이유
- Pod는 생성/삭제에 따라 IP가 계속 바뀜  
- 고정된 접근 지점을 제공하기 위해 **Service**가 필요함

---

## 핵심 기능들

### 1. 서비스 레지스트리 기능
- Pod에 label을 붙이고, Service에서 이 label로 selector 설정
- 그럼 자동으로 연결된 Pod IP들을 관리하는 **Endpoint 리소스**가 생성됨
- 연결된 Pod IP 목록은 자동 갱신됨

### 2. 서비스 로드밸런싱 기능
- kube-proxy가 Endpoint 상태를 모니터링 하다가  
  **iptables**나 **ipvs**를 업데이트함
- 클러스터 안의 트래픽이 Service IP로 들어오면  
  iptables가 자동으로 연결된 Pod IP로 라우팅함
- 여러 Pod이 연결돼 있으면 내부 알고리즘으로 분산 처리됨

### 3. 서비스 디스커버리 기능
- Cluster DNS(coredns)가 Service 이름을 IP로 변환해 줌  
  → 내부에서는 `service-name.namespace.svc.cluster.local` 형태로 접근 가능
- 같은 namespace면 이름만 써도 되고,  
  다른 namespace면 명시 필요

```bash
# 같은 namespace
curl http://my-service

# 다른 namespace
curl http://my-service.dev.svc.cluster.local
```

- StatefulSet + Headless Service 조합 시 Pod 이름으로 직접 접근 가능  
  (예: `pod-0.my-service.dev.svc.cluster.local`)

---

## 서비스 퍼블리싱 기능 (외부 노출)

| Type           | 설명                                             |
|----------------|--------------------------------------------------|
| ClusterIP      | 기본값. 클러스터 내부에서만 접근 가능            |
| NodePort       | 각 노드의 특정 포트로 접근 가능 (테스트용 등)    |
| LoadBalancer   | 외부 L4 장비나 클라우드 LoadBalancer 연결 가능   |
| ExternalName   | 외부 DNS 주소를 클러스터 내부 도메인처럼 사용 가능 |

---

## 기타 기능

### - 세션 어피니티 (Session Affinity)
- 클라이언트의 IP 기반으로 항상 같은 Pod에 트래픽을 전달
- 잘 안 씀 (요즘엔 Redis 같은 외부 저장소 사용)

### - 트래픽 폴리시
- 특정 Pod으로 트래픽을 제한하는 기능
- 제약 많아서 보통 Istio 같은 서비스 메시로 처리함

---

## port / targetPort / nodePort 감각

Service 볼 때 제일 자주 헷갈린 건 어느 포트가 어디를 뜻하는지였다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-svc
spec:
  type: NodePort
  selector:
    app: api
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 30080
```

- `port`: Service 자체가 받는 포트
- `targetPort`: Pod 안 컨테이너가 실제로 듣는 포트
- `nodePort`: 노드 IP로 외부에서 붙을 때 쓰는 포트

처음 볼 때는 `80 -> 8080`으로 포워딩한다고 생각하면 좀 덜 헷갈린다.

## Service 붙었는지 확인할 때 보는 것

Service를 만들었는데 접속이 안 되면 YAML만 다시 보기보다 selector와 endpoint를 먼저 보는 게 빠르다.

```bash
kubectl get svc
kubectl describe svc api-svc
kubectl get endpoints api-svc
```

- selector 라벨이 안 맞으면 Service는 떠 있어도 뒤에 연결된 Pod가 없음
- `endpoints`가 비어 있으면 Pod가 Ready 상태가 아니거나 라벨이 안 맞는 경우를 먼저 의심함
- NodePort인데 안 열리면 Service 문제 말고 보안 그룹이나 방화벽도 같이 봐야 함

## 요약

| 기능명              | 설명                                              |
|---------------------|---------------------------------------------------|
| 서비스 레지스트리   | Pod 레이블 기반으로 연결 관리                     |
| 서비스 로드밸런싱   | iptables 통해 Pod로 트래픽 분산                   |
| 서비스 디스커버리   | DNS 이름으로 Pod에 접근                           |
| 서비스 퍼블리싱     | 외부에서 접근 가능하도록 Service 타입 지정         |
| 세션 어피니티       | IP 기반 동일 Pod로 트래픽 유지 (거의 안 씀)        |
| 트래픽 폴리시       | 트래픽 제어 (Istio 등 서비스 메시로 대체 추천)     |

### endpoints가 비어 있을 때

- Service selector와 Pod label이 맞는지 먼저 확인
- Pod가 Ready 상태가 아니면 endpoint에 안 잡힐 수 있음
- `kubectl describe svc`와 `kubectl get endpoints`를 같이 보면 원인 찾기가 빠름


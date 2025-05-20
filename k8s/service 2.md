# Kubernetes Service

---

## 1. ClusterIP

- 가장 기본적인 Service 타입
- **Pod IP는 휘발성이므로**, Service를 통해 접근해야 안정적
- **Cluster 내부에서만 접근 가능**
- 여러 Pod에 트래픽을 라운드로빈 방식으로 분산

### 실습 예시

#### 1-1) Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
  labels:
    app: pod
spec:
  nodeSelector:
    kubernetes.io/hostname: k8s-worker1
  containers:
  - name: container
    image: kubetm/app
    ports:
    - containerPort: 8080
```

#### 1-2) ClusterIP Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-1
spec:
  selector:
    app: pod
  ports:
  - port: 9000
    targetPort: 8080
```

### 테스트 명령어

```bash
# Service IP 확인 후 테스트
curl <Service-IP>:9000/hostname
```

---

## 2. NodePort

- ClusterIP 기능 포함
- **외부에서 Worker Node의 IP + Port로 접근 가능**
- **테스트나 내부망 연동 용도**
- NodePort 포트 범위: **30000~32767**
- 명시하지 않으면 자동 할당됨

### 2-1) NodePort Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-2
spec:
  selector:
    app: pod
  ports:
  - port: 9000
    targetPort: 8080
    nodePort: 30001
  type: NodePort
```

### 2-2) Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-2
  labels:
    app: pod
spec:
  nodeSelector:
    kubernetes.io/hostname: k8s-worker2
  containers:
  - name: container
    image: kubetm/app
    ports:
    - containerPort: 8080
```

### 테스트 명령어

```bash
# 외부에서 직접 호출 가능
curl 192.168.56.31:30001/hostname
curl 192.168.56.32:30001/hostname
```

---

### 2-3) externalTrafficPolicy: Local

- NodePort에 옵션 추가 시, 트래픽이 해당 노드에 **실제로 존재하는 Pod로만 전달됨**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-3
spec:
  selector:
    app: pod
  ports:
  - port: 9000
    targetPort: 8080
    nodePort: 30002
  type: NodePort
  externalTrafficPolicy: Local
```

```bash
# 해당 노드에 존재하는 Pod로만 요청이 전달됨
curl 192.168.56.31:30002/hostname
curl 192.168.56.32:30002/hostname
```

---

## 3. LoadBalancer

- 클라우드 환경(GCP, AWS, Azure 등)에서만 사용 가능
- **외부 Load Balancer IP를 자동으로 생성**
- 내부 IP를 외부에 노출시키지 않음
- NodePort 방식과 기능 포함됨

### 3-1) LoadBalancer Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-4
spec:
  selector:
    app: pod
  ports:
  - port: 9000
    targetPort: 8080
  type: LoadBalancer
```

### 명령어

```bash
kubectl get service svc-4
```

---

## 리소스 정리

```bash
kubectl delete pod pod-1 pod-2
kubectl delete svc svc-1 svc-2 svc-3 svc-4
```

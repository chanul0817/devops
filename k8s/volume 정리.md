# Kubernetes Volume 정리

---

## 1. emptyDir

- Pod 내 여러 컨테이너 간 **임시 파일 공유용**
- Pod가 삭제되면 **데이터도 삭제됨**
- 임시 데이터 저장에 적합

```yaml
volumes:
- name: empty-dir
  emptyDir: {}
```

> 예: container1에서 생성한 파일을 container2에서 확인 가능

---

## 2. hostPath

- Node의 디렉토리를 Pod에 mount해서 사용
- Pod가 재시작되어도 데이터 유지
- **보안상 위험** → 운영 환경에서는 비추천
- 주로 테스트용

```yaml
volumes:
- name: host-path
  hostPath:
    path: /node-v
    type: DirectoryOrCreate
```

> 같은 Node에 있는 Pod끼리 `/node-v` 공유 가능

---

## 3. PVC / PV (Persistent Volume)

- **Pod → PVC → PV** 구조로 연결
- Pod가 삭제되어도 데이터는 유지됨
- 연결 조건:
  - 같은 `accessModes`
  - 같은 `storage` 용량 이상
- `storageClassName: ""` → 수동 연결 시 사용

### PersistentVolume

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-01
spec:
  capacity:
    storage: 1G
  accessModes:
  - ReadWriteOnce
  local:
    path: /node-v
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values: [k8s-worker1]
```

### PersistentVolumeClaim

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-01
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1G
  storageClassName: ""
```

### Pod에서 PVC 사용

```yaml
volumes:
- name: pvc-pv
  persistentVolumeClaim:
    claimName: pvc-01
```

---

## 4. Label + Selector를 통한 PV-PVC 매칭

- 여러 PV 중에서 특정 PV를 PVC에 **직접 지정해서 연결**

### PV

```yaml
metadata:
  labels:
    pv: pv-05
```

### PVC

```yaml
selector:
  matchLabels:
    pv: pv-05
```

---

## 리소스 삭제 순서

1. Pod
2. PVC
3. PV

```bash
kubectl delete pod pod-volume-1 pod-volume-2 ...
kubectl delete pvc pvc-01 pvc-02 ...
kubectl delete pv pv-01 pv-02 ...
```


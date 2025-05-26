# Kubernetes ConfigMap / Secret 기본 오브젝트 정리

## 공통 특징
- **ConfigMap**: 비밀 정보가 아닌 일반 설정 값 저장
- **Secret**: 민감한 정보 저장 (Base64 인코딩 필수, 보안에 유리)

---

## 1. Env 방식 (Literal)

### 1-1) ConfigMap

apiVersion: v1
kind: ConfigMap
metadata:
  name: cm-dev
data:
  SSH: "false"
  User: dev

# 명령어로 생성
kubectl create configmap cm-dev --from-literal=SSH=false --from-literal=User=dev

---

### 1-2) Secret

apiVersion: v1
kind: Secret
metadata:
  name: sec-dev
data:
  Key: MTIzNA==  # "1234"의 base64

# 명령어로 생성
kubectl create secret generic sec-dev --from-literal=Key=1234

---

### 1-3) Pod에서 주입

apiVersion: v1
kind: Pod
metadata:
  name: pod-1
spec:
  containers:
  - name: container
    image: kubetm/init
    envFrom:
    - configMapRef:
        name: cm-dev
    - secretRef:
        name: sec-dev

# → env 명령어로 주입 확인 가능

---

## 2. Env 방식 (File)

### 2-1) ConfigMap (파일 기반)

echo "Content" > file-c.txt
kubectl create configmap cm-file --from-file=./file-c.txt
# → 키: file-c.txt, 값: 파일 내용

---

### 2-2) Secret (파일 기반)

echo "Content" > file-s.txt
kubectl create secret generic sec-file --from-file=./file-s.txt
# → Base64 자동 인코딩됨

---

### 2-3) Pod에서 주입

apiVersion: v1
kind: Pod
metadata:
  name: pod-file
spec:
  containers:
  - name: container
    image: kubetm/init
    env:
    - name: file-c
      valueFrom:
        configMapKeyRef:
          name: cm-file
          key: file-c.txt
    - name: file-s
      valueFrom:
        secretKeyRef:
          name: sec-file
          key: file-s.txt

# → env 명령어로 확인 가능

---

## 3. Volume Mount 방식

- ConfigMap/Secret 수정 시 Pod 재시작 없이 즉시 반영됨

### 3-1) Pod

apiVersion: v1
kind: Pod
metadata:
  name: pod-mount
spec:
  containers:
  - name: container
    image: kubetm/init
    volumeMounts:
    - name: file-volume
      mountPath: /mount
  volumes:
  - name: file-volume
    configMap:
      name: cm-file

# → cd /mount && ls && cat file-c.txt로 확인

---

## 리소스 정리 명령어

kubectl delete pod pod-1 pod-file pod-mount
kubectl delete configmap cm-dev cm-file
kubectl delete secret sec-dev sec-file

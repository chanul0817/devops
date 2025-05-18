# 컨테이너 런타임(Container Runtime) 정리

---

## 정의
- **컨테이너 런타임**은 컨테이너를 실제로 **실행하고 관리**하는 소프트웨어
- 이미지 다운로드, 컨테이너 생성, 시작, 중지 등의 기능을 제공

---

## 주요 기능
| 기능 | 설명 |
|------|------|
| 이미지 Pull | DockerHub 등 Registry에서 이미지 다운로드 |
| 이미지 Unpack | 파일시스템에 이미지 압축 해제 |
| 컨테이너 생성 | 이미지 기반으로 프로세스 실행 환경 구성 |
| 컨테이너 시작/중지 | 실제 컨테이너 프로세스 시작, 중지, 삭제 |
| 로그/리소스 관리 | stdout, stderr, 메모리, CPU 등 모니터링 |

---

## 종류

### 1. Docker (구버전)
- 가장 많이 알려진 런타임
- **Containerd**와 **runc**를 내부적으로 사용
- 쿠버네티스에서는 현재 지원 중단됨 (Dockershim 제거됨)

---

### 2. Containerd (요즘 가장 일반적)
- CNCF에서 관리하는 경량 런타임
- Docker에서 런타임 기능만 따로 뺀 것
- Kubernetes와 잘 통합되고, 성능 좋음

---

### 3. CRI-O
- Kubernetes의 **CRI(Container Runtime Interface)**를 위해 만든 경량 런타임
- Open Container Initiative(OCI) 이미지 표준 지원
- Red Hat 계열에서 주로 사용 (ex. OpenShift)

---

### 4. runc
- 가장 하위의 런타임 (컨테이너를 fork/exec로 실행)
- 다른 런타임들이 내부적으로 이걸 사용함

---

## 계층 구조

```
Kubernetes (kubelet)
   ↓
Container Runtime Interface (CRI)
   ↓
컨테이너 런타임 (Containerd, CRI-O 등)
   ↓
저수준 런타임 (runc 등)
```

- kubelet은 CRI를 통해 런타임과 통신
- 실제 컨테이너 실행은 runc가 수행

---

## 쿠버네티스에서 런타임 설정 방법
- kubelet 실행 시 `--container-runtime-endpoint` 플래그로 소켓 지정
  예: `/run/containerd/containerd.sock`

---

## 정리 요약

| 런타임   | 특징 |
|----------|------|
| Docker   | 대중적이었지만 Kubernetes에선 이제 사용 안 함 |
| Containerd | 가볍고 안정적, Kubernetes에서 가장 많이 사용 |
| CRI-O    | CRI에 최적화, RedHat 계열에 많이 사용 |
| runc     | 컨테이너를 진짜로 실행하는 하위 실행기 |

--- 

## 참고
- Docker는 이제 "개발자용 도구"
- Kubernetes에서는 보통 **Containerd + runc** 조합으로 동작함

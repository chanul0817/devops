# FileStorage, BlockStorage, ObjectStorage 정리

## FileStorage (파일 단위 공유)
- **언제?**  
  여러 Pod들이 파일을 동시에 접근해야 할 때  
  (예: 한 Pod이 저장한 파일을 다른 Pod이 읽어야 하는 경우)

- **특징**  
  - 다수의 Pod가 동시에 연결 가능 (ReadWriteMany)
  - NFS 같은 외부 스토리지 사용
  - IO 병목 주의 (IOPS 고려해야 함)
  - App에서 파일 동시 접근 처리 필요

- **쿠버네티스에서 사용 방법**
  1. NFS 서버에 공유 디렉토리 구성
  2. `nfs` 타입의 PV 생성
  3. PVC로 PV 연결
  4. Deployment에 PVC 마운트

---

## BlockStorage (블록 단위, 고성능)
- **언제?**  
  빠른 디스크 성능이 필요한 경우  
  (예: DB, 로그 저장, 캐시 등)

- **특징**  
  - Pod 1개에 1볼륨 연결 (ReadWriteOnce)
  - 고성능, 저지연  
  - 파일 시스템은 OS나 컨테이너가 관리

- **쿠버네티스에서 사용 방법**
  1. 스토리지 솔루션 설치 (AWS EBS, Ceph, etc)
  2. CSI 드라이버 설치 + StorageClass 구성
  3. StatefulSet에 StorageClass 지정
  4. Pod 생성시 동적으로 PVC/PV 생성 → BlockStorage 연결

---

## ObjectStorage (API 기반 대용량 저장소)
- **언제?**  
  이미지, 백업 파일, 로그 아카이빙 등 대용량 저장 공간 필요할 때  
  (예: S3, MinIO)

- **특징**  
  - Pod와 직접 연결하지 않음 (PV, PVC 사용 안 함)
  - HTTP 기반 API로 파일 업/다운로드
  - 저장 용량 확장에 유리
  - 파일 I/O 속도는 느림

- **쿠버네티스에서 사용 방법**
  - MinIO 등 ObjectStorage 서버 구성
  - App에서 API로 직접 접근해서 사용

---

## 비교 요약

| 항목           | FileStorage       | BlockStorage       | ObjectStorage       |
|----------------|-------------------|--------------------|---------------------|
| 공유 방식      | 파일 공유         | 디스크 블록        | API 기반             |
| 연결 구조      | PV ↔ 여러 Pod     | PV ↔ 단일 Pod       | Pod ↔ API 호출        |
| 성능           | 중간 (IOPS 중요)  | 고성능              | 느림 (대용량 적합)    |
| 사용 예시      | 로그 수집, 공유파일 | DB, 고속처리         | 이미지 저장, 백업 등 |
| 쿠버네티스 사용| PV+PVC+nfs        | StorageClass+PVC   | 직접 API 호출        |

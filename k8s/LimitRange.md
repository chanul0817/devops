# Kubernetes LimitRange 요약

## 1. LimitRange란?
- Pod이 생성될 때, container.resources.limits와 requests가 없으면 기본값을 자동으로 설정해줌.
- 이미 생성된 Pod에는 적용되지 않음.

## 2. Deployment와 LimitRange
- Deployment에서 resources가 명시되지 않은 새 Pod이 생성될 때만 LimitRange가 적용됨.
- 이미 만들어진 Deployment에는 영향 없음.
- resources 필드가 있으면 LimitRange는 무시됨.

## 3. LimitRange 적용 실험 방법
- 기존 Deployment를 삭제 후 다시 생성해야 함.
  kubectl delete deploy myapp -n myspace
  kubectl apply -f deployment.yaml -n myspace

- 적용 여부 확인
  kubectl get pod -n myspace -o jsonpath="{.items[*].spec.containers[*].resources}"
  kubectl describe pod <pod-name> -n myspace

## 4. 주의사항
- deployment.yaml에 resources가 명시되어 있으면 LimitRange 기본값은 적용되지 않음.

## 5. 결론
- LimitRange는 새로 만들어지는 Pod에만 적용됨.
- resources가 명시되어 있으면 적용되지 않음.
- 실험할 땐 기존 Deployment 삭제 후 다시 생성해야 함.

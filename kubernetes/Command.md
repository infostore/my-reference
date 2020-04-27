# Command

```sh
kubectl apply -f deployment.yml --record=false
```

## Deployment

### Revision

```sh
kubectl rollout history deploy [deployment-name]
kubectl rollout undo deploy [deployment-name]
kubectl rollout undo deploy [deployment-name] --to-revision=3

# 되돌릴 수 있는 리비전은 디플로이먼트 템플릿에서 설정 - spec.revisionHistoryLimit (디폴트는 10)

# metadata.annotations.kubernetes.io/change-cause를 이용해 revision 정보 입력 가능
# annotation 정보가 제대로 동작하려면 --record=false 사용
kubectl apply -f deployment.yml --record=false
```
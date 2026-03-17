# cách tạo pod yml nhanh khi thi chứ không viết từ đầu
```bash
kubectl run <tên-pod> --image=<tên-image> --dry-run=client -o yaml > pod.yaml
```
```bash
kubectl create configmap app5-config --from-literal=APP_MODE=prod --dry-run=client -o yaml > app-configmap.yaml
```

```bash
kubectl create secret generic app5-secret --from-literal=API_KEY=secret123 --dry-run=client -o yaml > app-secret.yaml
```

Nhớ fieldPath phổ biến: metadata.name, status.podIP, spec.serviceAccountName, metadata.labels[‘key’]
```bash
kubectl get pod <pod-name> -o jsonpath='{.metadata.name}' # lấy tên pod
kubectl get pod <pod-name> -o jsonpath='{.status.podIP}' # lấy IP của pod
kubectl get pod <pod-name> -o jsonpath='{.spec.serviceAccountName}' # lấy service account name của pod
kubectl get pod <pod-name> -o jsonpath='{.metadata.labels['key']}' # lấy giá trị của label có key là 'key'
```
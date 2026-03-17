
# Cách gán kubectl cho termial
```bash
nano ~/.bashrc
source <(kubectl completion bash)
source ~/.bashrc
sau đó là có thể sử dụng được kubectl trên terminal như kubeclt get pods
``` 
# Pod
1. cách tạo pod yml nhanh khi thi chứ không viết từ đầu
```bash
kubectl run <tên-pod> --image=<tên-image> --dry-run=client -o yaml > pod.yaml
```
2. exec
```bash
# xem env của pod
kubectl exec -it <pod-name> -- env
# nếu pod có nhiều container thì phải chỉ rõ container nào muốn xem env
kubectl exec -it <pod-name> -c <container-name> -- env
# vào shell của pod
kubectl exec -it <pod-name> -- /bin/sh
# nếu pod có nhiều container thì phải chỉ rõ container nào muốn vào shell
kubectl exec -it <pod-name> -c <container-name> -- /bin/sh
```
3. xem log của pod
```bash
kubectl logs <pod-name>
# neu pod có nhiều container thì phải chỉ rõ container nào muốn xem log
kubectl logs <pod-name> -c <container-name>
```

4. xem describe của pod
```bash
kubectl describe pod <pod-name>
# nếu pod có nhiều container thì phải chỉ rõ container nào muốn xem describe
kubectl describe pod <pod-name> -c <container-name>
```
5. Nhớ fieldPath phổ biến: metadata.name, status.podIP, spec.serviceAccountName, metadata.labels[‘key’]
```bash
kubectl get pod <pod-name> -o jsonpath='{.metadata.name}' # lấy tên pod
kubectl get pod <pod-name> -o jsonpath='{.status.podIP}' # lấy IP của pod
kubectl get pod <pod-name> -o jsonpath='{.spec.serviceAccountName}' # lấy service account name của pod
kubectl get pod <pod-name> -o jsonpath='{.metadata.labels['key']}' # lấy giá trị của label có key là 'key'
```
6. Lệnh cơ bản để giám sát log
```bash
kubectl logs -f <pod-name>
# nếu pod có nhiều container thì phải chỉ rõ container nào muốn xem log
kubectl logs -f <pod-name> -c <container-name>
# log từ previous container (nếu container đã bị restart)
kubectl logs -f <pod-name> -c <container-name> --previous
# limit line log
kubectl logs -f <pod-name> -c <container-name> --tail=100
# time-stamp log
kubectl logs -f <pod-name> -c <container-name> --timestamps
# sine log từ thời điểm hiện tại
kubectl logs -f <pod-name> -c <container-name> --since=10m
```
7. lệnh cơ bản giám sát event của pod
```bash
# liêt kê tất cả event
kubectl get events
# all namespace
kubectl get events --all-namespaces
# watch real-time event
kubectl get events -w
# sort event theo thời gian
kubectl get events --sort-by=.metadata.creationTimestamp
# output format
kubectl get events -o wide
# filter
kubectl get events -l app=myapp
# event theo pod
kubectl get events --field-selector involvedObject.name=<pod-name>
```
# ConfigMap và Secret
1. tạo secret nhanh
```bash
kubectl create secret generic my-secret --from-literal=username=admin --from-literal=password=password123 --dry-run=client -o yaml > my-secret.yaml
```
2. tạo configmap nhanh
```bash
kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2 --dry-run=client -o yaml > my-config.yaml
```

3. edit configmap
```bash
kubectl edit configmap my-config
# sau khi edit xong thì phải xóa pod đi để nó tạo lại với config mới
kubectl delete pod <pod-name>
kubectl apply -f my-pod.yaml
```
4. edit secret
```bash
kubectl edit secret my-secret
# sau khi edit xong thì phải xóa pod đi để nó tạo lại với config mới
kubectl delete pod <pod-name>
kubectl apply -f my-pod.yaml
```

# Deployment
1. cách tạo deployment nhanh
```bash
kubectl create deployment nginx-deployment --image=nginx --dry-run=client -o yaml > deployment.yaml
```
2. scale deployment
```bash
kubectl scale deployment nginx-deployment --replicas=5
```
3. update image của deployment
```bash
kubectl set image deployment/nginx-deployment <container-name>=nginx:latest
```
4. update image
```bash
kubectl set image deployment/nginx-deployment <container-name>=nginx:latest --record
```
5. kiem tra rollout deployment
```bash
kubectl rollout status deployment/nginx-deployment
```
6. rollback deployment
```bash
kubectl rollout undo deployment/nginx-deployment
```
7. xem lịch sử rollout
```bash
kubectl rollout history deployment/nginx-deployment
```

# Service
1. cách tạo service nhanh
```bash 
kubectl expose deployment nginx --port=80 --type=NodePort --dry-run=client -o yaml > service.yaml
```
# Namespace
1. tạo namespace nhanh
```bash
kubectl create namespace my-namespace --dry-run=client -o yaml > my-namespace.yaml
```


# Liveness, Readiness, Startup Probe
1. liveness : kiểm tra xem container có đang sống hay không, nếu không sống thì sẽ restart container
2. readiness: kiểm tra xem container có sẵn sàng để nhận traffic hay không, nếu không sẵn sàng thì sẽ không gửi traffic đến container đó
3. startup: kiểm tra xem container có khởi động thành công hay không, nếu không khởi động thành công thì sẽ restart container
4. Các tham số phổ biến của probe:
- initialDelaySeconds: thời gian chờ trước khi bắt đầu probe
- periodSeconds: thời gian giữa các lần probe
- timeoutSeconds: thời gian chờ response của probe
- failureThreshold: số lần probe thất bại trước khi coi là container không sống/sẵn sàng/khởi động không thành công
- successThreshold: số lần probe thành công trước khi coi là container sống/sẵn sàng/khởi động thành công
5. Mẫu probe:
```yaml
livenessProbe:
  httpGet:
    path: /
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
readinessProbe:
  httpGet:
    path: /
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
startupProbe:
  httpGet:
    path: /
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```

# Cách gán kubectl cho termial
nano ~/.bashrc
```
source <(kubectl completion bash)
alias k=kubectl
complete -F __start_kubectl k
alias kgp='kubectl get pod -o wide'
alias kdp='kubectl describe pod'
```
source ~/.bashrc

sau đó là có thể sử dụng được kubectl trên terminal như kubeclt get pods
 
# Pod
1. cách tạo pod yml nhanh khi thi chứ không viết từ đầu
```bash
kubectl run <tên-pod> --image=<tên-image> --dry-run=client -o yaml > pod.yaml
co namespace
kubectl run <tên-pod> --image=<tên-image> -n <namespace> --dry-run=client -o yaml > pod.yaml
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
kubectl create secret generic <secret-name> --from-literal=username=admin --from-literal=password=password123 --dry-run=client -o yaml > my-secret.yaml
```
2. tạo configmap nhanh
```bash
kubectl create configmap <configmap-name> --from-literal=key1=value1 --from-literal=key2=value2 --dry-run=client -o yaml > my-config.yaml
```

3. edit configmap
```bash
kubectl edit configmap <configmap-name>
# sau khi edit xong thì phải xóa pod đi để nó tạo lại với config mới
kubectl delete pod <pod-name>
kubectl apply -f my-pod.yaml
```
4. edit secret
```bash
kubectl edit secret <secret-name>
# sau khi edit xong thì phải xóa pod đi để nó tạo lại với config mới
kubectl delete pod <pod-name>
kubectl apply -f my-pod.yaml
```

# Deployment
1. cách tạo deployment nhanh
```bash
kubectl create deployment <deployment-name> --image=nginx --dry-run=client -o yaml > deployment.yaml
```
2. scale deployment
```bash
kubectl scale deployment/<deployment-name> --replicas=5
```
3. update image của deployment
```bash
kubectl set image deployment/<deployment-name> <container-name>=nginx:latest
```
4. update image
```bash
kubectl set image deployment/<deployment-name> <container-name>=nginx:latest --record
```
5. kiem tra rollout deployment
```bash
kubectl rollout status deployment/<deployment-name>
```
6. rollback deployment
```bash
kubectl rollout undo deployment/<deployment-name>
```
7. xem lịch sử rollout
```bash
kubectl rollout history deployment/<deployment-name>
```

# Service
1. cách tạo service nhanh
```bash
kubectl expose deployment <deployment-name> --port=80 --target-port=80 --dry-run=client -o yaml > service.yaml
```
2. lưu ý
```bash
port: là cổng mà service sẽ expose ra bên ngoài
targetPort: là cổng mà container trong pod đang lắng nghe, nếu không chỉ rõ targetPort thì mặc định sẽ là port
nodePort: là cổng mà service sẽ expose ra trên node, nếu không chỉ rõ nodePort thì Kubernetes sẽ tự động chọn một cổng trong khoảng 30000-32767
```
# Namespace
1. tạo namespace nhanh
```bash
kubectl create namespace <namespace-name> --dry-run=client -o yaml > <namespace-name>.yaml
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
apiVersion: v1
kind: Pod
metadata:
  name: liveness-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ['/bin/sh']
    args: ['-c', 'while true; do echo hello; sleep 10;done']
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

# HPA
Cảnh báo thi CKAD: Nếu Pod không có requests.cpu => HPA sẽ không hoạt động => Mất điểm.
luôn dùng apiVersion: autoscaling/v2  
Không quên requests.cpu
1. lệnh tạo HPA nhanh
```bash
kubectl autoscale deployment <deployment-name> --cpu=50% --min=1 --max=10 --dry-run=client -o yaml > hpa.yaml
```
2. xem HPA
```bash
kubectl get hpa
```
3. xem describe HPA
```bash
kubectl describe hpa <hpa-name>
```
4. xem scale target của HPA
```bash
kubectl get hpa <hpa-name> -o jsonpath='{.spec.scaleTargetRef.name}'
```
5. xem current replicas của HPA
```bash
kubectl get hpa <hpa-name> -o jsonpath='{.status.currentReplicas}'
```
6. sửa HPA
```bash
kubectl edit hpa <hpa-name>
```

# Ingress
1. cách tạo ingress nhanh
```bash
kubectl create ingress <ingress-name> --rule='host/path=service:port' --dry-run=client -o yaml > ingress.yaml
```
2. xem ingress
```bash
kubectl get ingress
```
3. xem describe ingress
```bash
kubectl describe ingress <ingress-name>
```
4. xem rules của ingress
```bash
kubectl get ingress <ingress-name> -o jsonpath='{.spec.rules[*].host}'
```
5. sửa ingress
```bash
kubectl edit ingress <ingress-name>
```

# network policy


# PV PVC
1. cách tạo pv 
```bash
# cần minikube ssh để tạo thư mục /mnt/data trên node minikube sudo mkdir -p /mnt/data
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: /mnt/data
```
2. cách tạo pvc
```bash
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-pvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: manual
```
3. dymamic provisioning
```bash
# PVC có storageClassName: local-storage sẽ tự động tạo PV dynamic nếu có provisioner.
#CKAD Tip: Khi đề nói “use default storage class”, chỉ cần tạo PVC có storageClassName: "" hoặc bỏ field này.
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
reclaimPolicy: Delete
```

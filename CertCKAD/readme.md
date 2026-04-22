```bash
  ██████╗██╗  ██╗ █████╗ ██████╗ 
 ██╔════╝██║ ██╔╝██╔══██╗██╔══██╗
 ██║     █████╔╝ ███████║██║  ██║
 ██║     ██╔═██╗ ██╔══██║██║  ██║
 ╚██████╗██║  ██╗██║  ██║██████╔╝
  ╚═════╝╚═╝  ╚═╝╚═╝  ╚═╝╚═════╝ 
```
<a id="kubectl-terminal"></a>
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

# Menu

- [Cách gán kubectl cho terminal](#kubectl-terminal)
- [Pod](#pod)
- [ConfigMap và Secret](#configmap-secret)
- [Deployment](#deployment)
- [Service](#service)
- [Namespace](#namespace)
- [Liveness, Readiness, Startup Probe](#probes)
- [HPA](#hpa)
- [Ingress](#ingress)
- [Network Policy](#network-policy)
- [PV PVC](#pv-pvc)
- [RBAC](#rbac)
- [Rolling Update && Rollback](#rolling-update-rollback)
- [Quota](#quota)
- [CronJob](#cronjob)
- [Security Context](#security-context)
- [Canary Deployment](#canary-deployment)

<a id="pod"></a>
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
<a id="configmap-secret"></a>
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

<a id="deployment"></a>
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

<a id="service"></a>
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
<a id="namespace"></a>
# Namespace

1. tạo namespace nhanh
```bash
kubectl create namespace <namespace-name> --dry-run=client -o yaml > <namespace-name>.yaml
```


<a id="probes"></a>

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

<a id="hpa"></a>
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

<a id="ingress"></a>
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

<a id="network-policy"></a>
# Network Policy

1. cách tạo network policy 
  - deny-all
```bash
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: net-test
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```
  - allow specific pod + port
```bash
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-http
  namespace: net-test
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 80
```
  - allow specific namespace
```bash
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-namespace
  namespace: net-test
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: frontend-namespace
    ports:
    - protocol: TCP
      port: 80
```

2. xem network policy
```bash
kubectl get networkpolicy
```
3. xem describe network policy
```bash
kubectl describe networkpolicy <network-policy-name>
```
4. xem pod selector của network policy
```bash
kubectl get networkpolicy <network-policy-name> -o jsonpath='{.spec.podSelector.matchLabels}'
```
5. xem ingress rules của network policy
```bash
kubectl get networkpolicy <network-policy-name> -o jsonpath='{.spec.ingress[*].from[*].podSelector.matchLabels}'
```

<a id="pv-pvc"></a>
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

<a id="rbac"></a>
# RBAC

1. cách tạo role nhanh
```bash
kubectl create role <role-name> --verb=get,list,watch --resource=pods -n <namespace> --dry-run=client -o yaml > role.yaml
```
2 . tạo service account nhanh
```bash
kubectl create serviceaccount <serviceaccount-name> -n <namespace> --dry-run=client -o yaml > serviceaccount.yaml
```
3. cách tạo rolebinding nhanh
```bash
kubectl create rolebinding <rolebinding-name> --role=<role-name> --serviceaccount=<namespace>:<serviceaccount-name> -n <namespace> --dry-run=client -o yaml > rolebinding.yaml
```
4. thêm quyền cho service account vào pod
```yaml
spec:  
  serviceAccountName: app-sa
```
5. flow lab theo folder `rbac/`
```bash
kubectl create ns rbac-test
kubectl apply -f rbac/sa.yaml
kubectl apply -f rbac/role.yaml
kubectl apply -f rbac/rolebind.yaml
kubectl apply -f rbac/pod.yaml

kubectl -n rbac-test get sa,role,rolebinding,pod
kubectl -n rbac-test auth can-i list pods --as=system:serviceaccount:rbac-test:app-sa
```

<a id="rolling-update-rollback"></a>
# Rolling Update && Rollback

0. trong yaml deployment, cần có field `strategy.type: RollingUpdate` để có thể sử dụng rolling update
```bash
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1 # số pod tối đa được phép chết
      maxSurge: 1 # số lượng pod mới tối đa có thể được tạo ra trong quá trình update
# replicas = 3 thì tạo thêm 1 pod mới, xóa 1 pod cũ => luôn đảm bảo có 3 pod chạy trong quá trình update
```
1. rolling update
```bash
kubectl set image -n <namespace> deployment <deployment-name> <container-name>=<image-name> 
```
2. theo dõi rollout status
```bash
kubectl rollout status -n <namespace> deployment <deployment-name>
```
3. history rollout
```bash
kubectl rollout history -n <namespace> deployment <deployment-name>
```
4. rollback
```bash
kubectl rollout undo -n <namespace> deployment <deployment-name>
# rollback về revision cụ thể
kubectl rollout undo -n <namespace> deployment <deployment-name> --to-revision=<revision-number>
```
5. flow lab theo folder `rollingUpdate-Rollback/`
```bash
kubectl create ns rolling
kubectl apply -f rollingUpdate-Rollback/depl.yaml

kubectl -n rolling set image deployment/myapp nginx=nginx:1.26 --record
kubectl -n rolling rollout status deployment/myapp
kubectl -n rolling rollout history deployment/myapp
kubectl -n rolling rollout undo deployment/myapp
```

<a id="quota"></a>
# Quota

1. cách tạo resource quota nhanh
```bash
kubectl create quota <quota-name> --hard=requests.cpu=200m,requests.memory=512Mi,pods=10 -n <namespace> --dry-run=client -o yaml > quota.yaml
```
2. xem resource quota
```bash
kubectl get quota -n <namespace>
```
3. xem describe resource quota
```bash
kubectl describe quota <quota-name> -n <namespace>
```
4. sửa resource quota
```bash
kubectl edit quota <quota-name> -n <namespace>
```
5. cách tạo limit range nhanh
```bash
apiVersion: v1
kind: LimitRange
metadata:
  name: limit-test
  namespace: quota-ns
spec:
  limits:
  - type: Container
    default:
      cpu: "300m"
      memory: "256Mi"
    defaultRequest:
      cpu: "200m"
      memory: "128Mi"
    min:
      cpu: "100m"
      memory: "64Mi"
    max:
      cpu: "400m"
      memory: "512Mi"
```
6. flow lab theo folder `resourceQuotaLimitRange/`
```bash
kubectl create ns quota-ns
kubectl apply -f resourceQuotaLimitRange/quota.yaml
kubectl apply -f resourceQuotaLimitRange/limitrange.yaml
kubectl -n quota-ns get quota,limitrange

# pod này request cpu=50m < min 100m nên sẽ bị từ chối
kubectl apply -f resourceQuotaLimitRange/bad-pod.yaml
kubectl -n quota-ns describe pod bad-pod
```

<a id="cronjob"></a>
# cronjob

1. cách tạo cronjob nhanh
```bash
kubectl create cronjob <cronjob-name> --image=busybox --schedule="*/5 * * * *" --dry-run=client -o yaml > cronjob.yaml

schedule: '*/1 * * * *'
concurrencyPolicy: Forbid
successfulJobsHistoryLimit: 3
failedJobsHistoryLimit: 1
#các tham số cùng cấp với schedule
```
2. xem cronjob
```bash
kubectl get cronjob
```
3. xem describe cronjob
```bash
kubectl describe cronjob <cronjob-name>
```
4. sửa cronjob
```bash
kubectl edit cronjob <cronjob-name>
```
5. flow lab theo folder `cronjob/`
```bash
kubectl apply -f cronjob/cronjob.yaml
kubectl get cronjob
kubectl describe cronjob time-job
kubectl get jobs --watch
```

<a id="security-context"></a>
# security context

1. cách tạo pod với security context
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sec-pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
    runAsNonRoot: true
  containers:
  - image: ubuntu
    name: sec-pod
    command: ["sleep", "3600"]
    resources: {}
    securityContext:
      allowPrivilegeEscalation: false
      capabilities:
        drop:
          - ALL
        add:
          - NET_ADMIN
      privileged: false
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```
2. lưu ý theo lab hiện tại trong folder `security/`
```bash
# security/pod.yaml đang dùng runAsUser: 0 để demo quyền và capability drop
kubectl apply -f security/pod.yaml
kubectl get pod sec-pod
kubectl describe pod sec-pod
```
3. best practice khi đề yêu cầu hardening
```yaml
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
containers:
- securityContext:
    allowPrivilegeEscalation: false
    privileged: false
    capabilities:
      drop: ["ALL"]
```

<a id="canary-deployment"></a>
# canary deployment

1. cách tạo canary deployment y như deployment chỉ là thêm trường version vào label để phân biệt với stable deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-app
    version: v1
  name: my-app-v1
spec:
  replicas: 4
  selector:
    matchLabels:
      app: my-app
      version: v1
  strategy: {}
  template:
    metadata:
      labels:
        app: my-app
        version: v1
    spec:
      containers:
      - image: nginx:1.25
        name: nginx
        ports: 
        - containerPort: 80
        resources: {}
status: {}
# tạo thêm deployment y hệt với version: v2, image: nginx:1.26, replicas: 1 
```
2. flow lab theo folder `canary/`
```bash
kubectl apply -f canary/v1.yaml
kubectl apply -f canary/v2.yaml
kubectl apply -f canary/svc.yaml

kubectl get deploy my-app-v1 my-app-v2
kubectl get svc my-service
kubectl get pods -l app=my-app --show-labels
```
3. lưu ý để split traffic dễ kiểm soát
```bash
# Service selector nên match label chung của cả stable + canary
selector:
  app: my-app

# Tỉ lệ traffic xấp xỉ theo số replicas (ví dụ 4:1 ~ 80/20)
```

# Tổng hợp nhanh theo folder mới
```bash
canary/ -> v1.yaml, v2.yaml, svc.yaml
cronjob/ -> cronjob.yaml
rbac/ -> sa.yaml, role.yaml, rolebind.yaml, pod.yaml
resourceQuotaLimitRange/ -> quota.yaml, limitrange.yaml, bad-pod.yaml
rollingUpdate-Rollback/ -> depl.yaml
security/ -> pod.yaml
```

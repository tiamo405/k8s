# CKAD Certification Study Materials 📚

Bộ tài liệu tổng hợp toàn bộ kiến thức và ví dụ YAML cần thiết để vượt qua kỳ thi **Certified Kubernetes Application Developer (CKAD)**.

---

## 📂 Cấu trúc Tệp

### 1. **01-deployment-comprehensive.yaml** 🚀
**Nhiều deployment nhỏ để học theo từng kiểu env, volume và probes**

Bao gồm:
- Deployment tách riêng theo env: literal, ConfigMap, Secret, downwardAPI
- ConfigMap (literal data, file data)
- Secret (generic, stringdata)
- PersistentVolume và PersistentVolumeClaim
- **Tất cả 6 loại volume mount:**
  - emptyDir (tạm thời, mất khi pod terminate)
  - hostPath (từ node filesystem)
  - ConfigMap (file config)
  - Secret (dữ liệu nhạy cảm)
  - PersistentVolumeClaim (storage persistent)
  - downwardAPI (pod metadata dưới dạng file)
- Environment variables từ 4 nguồn (literal, ConfigMap, Secret, fieldRef)
- Probes gom chung trong 1 deployment riêng
- Resource requests/limits (QUAN TRỌNG cho HPA)

**🎯 Cách sử dụng:**
```bash
# Tạo tất cả resources
kubectl apply -f 01-deployment-comprehensive.yaml

# Kiểm tra
kubectl get deployment
kubectl get pod
kubectl describe deployment app-env-literal
kubectl describe deployment app-probes
```

---

### 2. **02-pod-comprehensive.yaml** 🎪
**Các loại Pod - từ cơ bản đến advanced**

Bao gồm:
- Single-container pod với tất cả volume types
- Multi-container pod (sidecar pattern)
- Pod với tất cả 3 loại probe (startup, liveness, readiness)
- Pod với exec probe, TCP probe, HTTP probe
- Pod với resource limits (QoS classes)
- Pod với affinity và node selector
- Pod với security context
- Pod với envFrom (load tất cả env từ ConfigMap/Secret)

**🎯 Cách sử dụng:**
```bash
# Tạo pod
kubectl apply -f 02-pod-comprehensive.yaml

# Xem logs
kubectl logs comprehensive-pod

# Exec vào pod
kubectl exec -it comprehensive-pod -- /bin/sh

# Xem events (probe status)
kubectl get events
```

---

### 3. **03-service-comprehensive.yaml** 🌐
**Tất cả loại Service và cách sử dụng**

Bao gồm:
- **ClusterIP:** Internal communication (default)
- **NodePort:** Access từ ngoài cluster qua node:port
- **LoadBalancer:** External load balancer (cloud)
- **ExternalName:** Route đến external DNS name
- **Headless Service:** Cho StatefulSet, DNS names
- **Service với External IPs**
- **Service với manual Endpoints**
- **Multi-port service**

**🎯 Cách sử dụng:**
```bash
# Tạo service từ deployment
kubectl expose deployment <name> --port=80 --target-port=8080 --type=ClusterIP

# Hoặc apply từ file
kubectl apply -f 03-service-comprehensive.yaml

# Test kết nối
kubectl run -it --rm debug --image=busybox -- wget -q -O- http://app-service-clusterip
```

---

### 4. **04-configmap-secret-comprehensive.yaml** 🔐
**ConfigMap và Secret - tất cả cách tạo và sử dụng**

Bao gồm:
- **ConfigMap:** Từ literals, từ file, multi-file
- **Secret:** Generic, docker-registry, TLS, stringdata
- Pod sử dụng ConfigMap (env + volume)
- Pod sử dụng Secret (env + volume)
- Pod với docker registry secret (private images)
- Pod với envFrom (tất cả keys thành env vars)

**🎯 Cách tạo nhanh:**
```bash
# ConfigMap từ literal
kubectl create configmap app-config --from-literal=key=value --dry-run=client -o yaml

# Secret từ literal
kubectl create secret generic app-secret --from-literal=key=value --dry-run=client -o yaml

# Secret từ file
kubectl create secret generic app-secret --from-file=/path/to/file --dry-run=client -o yaml

# TLS Secret
kubectl create secret tls tls-secret --cert=tls.crt --key=tls.key --dry-run=client -o yaml
```

**🎯 Edit sau khi tạo:**
```bash
kubectl edit configmap app-config
kubectl edit secret app-secret
# Pod phải xóa và recreate để lấy config mới (env vars)
# Hoặc mount dưới dạng volume (tự động update)
```

---

### 5. **05-storage-pv-pvc-comprehensive.yaml** 💾
**Storage - PV, PVC, StorageClass, Dynamic Provisioning**

Bao gồm:
- **StorageClass:** Manual, Local, Dynamic (EBS, EBS-gp2, GCP)
- **PersistentVolume:** hostPath, NFS, AWS EBS, GCP
- **PersistentVolumeClaim:** Basic, với selector, dynamic
- Pod với PVC
- Deployment với PVC (note: replicas=1 vì RWO)
- StatefulSet với PVC (mỗi pod có PVC riêng)
- Pod với multiple volumes

**🎯 Quan trọng cho thi:**
- StatefulSet tự động tạo PVC thông qua `volumeClaimTemplates`
- `accessModes` quyết định pod nào có thể dùng
  - RWO (ReadWriteOnce): 1 pod
  - ROX (ReadOnlyMany): nhiều pod read
  - RWX (ReadWriteMany): nhiều pod read/write (NFS)

---

### 6. **06-probes-comprehensive.yaml** 🏥
**Liveness, Readiness, Startup Probes - Thực hành**

Bao gồm:
- HTTP GET probes (most common)
- TCP Socket probes (databases, message queues)
- EXEC probes (run command)
- Startup probe (cho slow-starting apps)
- Pod với tất cả 3 probe types
- Multi-container pod với khác nhau probes
- Deployment với probes

**🎯 Hiểu rõ:**
- **Startup:** Chạy trước, cho app khởi động (30s)
- **Liveness:** Container còn sống? Restart nếu fail
- **Readiness:** Container sẵn sàng? Stop traffic nếu fail
- **Probe response:**
  - HTTP 200-399: Success
  - Exit code 0 (exec): Success
  - Other: Failure

**🎯 Testing probes:**
```bash
kubectl port-forward pod/<name> 8080:80
curl http://localhost:8080/health

kubectl exec -it pod/<name> -- test -f /tmp/healthy
echo $?  # 0=success, 1=failure
```

---

### 7. **07-hpa-comprehensive.yaml** 📈
**Horizontal Pod Autoscaler - Scaling tự động**

Bao gồm:
- Deployment với resource requests (BẮTBUỘC!)
- HPA V2 (CPU-based, memory-based, combined)
- HPA với custom metrics
- HPA với behavior customization (scale up fast, down slow)
- StatefulSet với HPA
- Job với HPA

**🎯 CRITICAL UNTUK THI:**
```yaml
# Pod PHẢI có resource requests
resources:
  requests:
    cpu: "100m"      # HPA dùng cái này để tính %
    memory: "128Mi"
  limits:
    cpu: "200m"
    memory: "256Mi"
```

**🎯 HPA tính toán:**
```
HPA scales khi:
current_cpu_usage / requested_cpu > targetCPUUtilizationPercentage
```

**🎯 Kiểm tra HPA:**
```bash
kubectl get hpa
kubectl describe hpa app-hpa
kubectl top pods
```

---

### 8. **08-ingress-networkpolicy-comprehensive.yaml** 🛡️
**Ingress (external access) và NetworkPolicy (security)**

#### **INGRESS:**
- Simple ingress (single backend)
- Path-based routing (`/api`, `/web`)
- Host-based routing (virtual hosts)
- TLS/HTTPS ingress
- Ingress với annotations

**🎯 Tạo nhanh:**
```bash
kubectl create ingress web --rule="web.example.com/=web-service:80" --dry-run=client -o yaml
```

#### **NETWORKPOLICY:**
- Default deny all (secure by default)
- Allow từ specific pods
- Allow từ specific namespaces
- Allow specific egress (chiều ra)
- Combined ingress + egress
- IPBLOCK (CIDR ranges)

**🎯 Policy types:**
```
policyTypes:
- Ingress     # Incoming traffic
- Egress      # Outgoing traffic
```

**🎯 Test connectivity:**
```bash
# From pod A try reach pod B
kubectl exec -it pod-a -- curl http://pod-b-ip:8080

# If blocked by policy, timed out
```

---

### 9. **09-statefulset-comprehensive.yaml** 📊
**StatefulSet - Cho ứng dụng stateful (DB, queue, cache)**

Bao gồm:
- Headless Service (BẮTBUỘC)
- MySQL StatefulSet
- PostgreSQL StatefulSet
- StatefulSet với init container
- StatefulSet với update strategy
- Cassandra StatefulSet (phức tạp)
- PVC automatic thông qua volumeClaimTemplates

**🎯 Khác với Deployment:**
- **Pod names:** Ordered (mysql-0, mysql-1, mysql-2...)
- **DNS names:** Stable (mysql-0.mysql-service...)
- **Storage:** Linked to pod identity
- **Scaling:** Ordered (không random)

**🎯 Tạo StatefulSet:**
```bash
kubectl create statefulset web --image=nginx \
  --replicas=3 --dry-run=client -o yaml > sts.yaml
```

**🎯 Kiểm tra PVC của StatefulSet:**
```bash
kubectl get pvc
# PVC names: <volumeClaimTemplate-name>-<pod-name>
# Example: data-postgres-statefulset-0
```

---

### 10. **10-other-resources-comprehensive.yaml** 🔧
**Job, DaemonSet, Namespace, PDB, Cluster RBAC mẫu chung**

Bao gồm:
- **Job:** One-time batch work, parallelism, array jobs
- **DaemonSet:** Run trên mỗi node (logging, monitoring)
- **Namespace:** Resource isolation
- **PodDisruptionBudget:** Avoid disruptions
- **RBAC mẫu chung:** Role/RoleBinding/ServiceAccount/ClusterRole

**🎯 Job keywords:**
- `completions`: Số lần chạy cần thành công
- `parallelism`: Bao nhiêu pod chạy song song
- `backoffLimit`: Retry count trước khi fail

**🎯 DaemonSet keywords:**
- Chạy trên mỗi node (hoặc node với label)
- Dùng tolerations cho tainted nodes
- Dùng nodeSelector để filter

---

### 11. **11-exam-cheatsheet.md** 📝
**Comprehensive cheatsheet với tất cả lệnh và tips**

**Bao gồm:**
- kubectl basic commands
- Debugging commands
- Deployment scaling
- Resource creation patterns from CLI
- vim tips and tricks
- Common exam mistakes
- Time management tips
- Study focus areas

**🎯 Dùng nó để:**
- Quick reference trong khi học
- Ghi nhớ lệnh hay dùng
- Avoid common mistakes
- Time management strategies

---

### 12. **12-canary-comprehensive.yaml** 🧪
**Canary deployment riêng**

Bao gồm:
- **Stable + Canary:** v1/v2 deployments
- **Traffic split:** service selector chung theo label `app`

**🎯 Cách sử dụng:**
```bash
kubectl apply -f 12-canary-comprehensive.yaml
kubectl get deploy,svc
kubectl get pods -l app=my-app --show-labels
```

---

### 13. **13-rolling-update-rollback-comprehensive.yaml** 🔁
**Rolling update và rollback riêng**

Bao gồm:
- Namespace `rolling`
- Deployment có `strategy.type: RollingUpdate`
- Command mẫu cho `rollout status/history/undo`

---

### 14. **14-security-context-comprehensive.yaml** 🔒
**Security context riêng**

Bao gồm:
- Mẫu theo lab hiện tại (`runAsUser: 0`)
- Mẫu hardened (`runAsNonRoot`, drop ALL capabilities)

---

### 15. **15-cronjob-comprehensive.yaml** ⏰
**CronJob riêng theo folder `cronjob/`**

Bao gồm:
- `schedule: '*/1 * * * *'`
- `concurrencyPolicy: Forbid`
- `successfulJobsHistoryLimit` và `failedJobsHistoryLimit`

---

### 16. **16-rbac-comprehensive.yaml** 👮
**RBAC riêng theo folder `rbac/`**

Bao gồm:
- Namespace `rbac-test`
- ServiceAccount `app-sa`
- Role + RoleBinding
- Pod test dùng service account

---

### 17. **17-resourcequota-limitrange-comprehensive.yaml** 📏
**ResourceQuota + LimitRange riêng theo folder `resourceQuotaLimitRange/`**

Bao gồm:
- Namespace `quota-ns`
- `ResourceQuota` và `LimitRange`
- `bad-pod` để test reject theo min CPU

---

## 🚀 CÁCH DÙNG BỘ TÀI LIỆU NÀY

### **Tuần 1: Bộ Frameworks**
1. Đọc 01-deployment-comprehensive.yaml - hiểu deployment
2. Làm bài tập: Copy, modify, apply
3. Kiểm tra: `kubectl describe`, `kubectl logs`

```bash
cp 01-deployment-comprehensive.yaml my-test.yaml
# Edit trong VIM
kubectl apply -f my-test.yaml
kubectl get all
kubectl describe deployment app-env-literal
kubectl describe deployment app-probes
```

### **Tuần 2: Volumes & Storage**
1. Đọc 04-configmap-secret-comprehensive.yaml
2. Đọc 05-storage-pv-pvc-comprehensive.yaml
3. Làm bài tập: Create, mount, verify

```bash
# Create ConfigMap
kubectl create configmap my-app --from-literal=key=value
kubectl apply -f 01-deployment-comprehensive.yaml
kubectl exec -it <pod> -- cat /etc/config/app-name
```

### **Tuần 3: Probes & HPA**
1. Đọc 06-probes-comprehensive.yaml
2. Đọc 07-hpa-comprehensive.yaml
3. Làm bài tập: Create probes, test with port-forward

```bash
kubectl apply -f 01-deployment-comprehensive.yaml
kubectl describe pod <name>  # Xem probe status
kubectl port-forward pod/<name> 8080:80
curl http://localhost:8080/health
```

### **Tuần 4: Services & Networking**
1. Đọc 03-service-comprehensive.yaml
2. Đọc 08-ingress-networkpolicy-comprehensive.yaml
3. Làm bài tập: Create services, test connectivity

```bash
kubectl expose deployment <name> --port=80 --target-port=8080 --type=NodePort
kubectl get svc
kubectl run -it --rm debug --image=busybox -- wget -q -O- http://<service>:80
```

### **Tuần 5: Advanced Resources**
1. Đọc 09-statefulset-comprehensive.yaml
2. Đọc 10-other-resources-comprehensive.yaml
3. Đọc 12-canary-comprehensive.yaml
4. Đọc 13-rolling-update-rollback-comprehensive.yaml
5. Đọc 14-security-context-comprehensive.yaml

```bash
# StatefulSet has ordered pod names
kubectl apply -f 09-statefulset-comprehensive.yaml
kubectl get pods --sort-by=.metadata.name
kubectl get pvc  # Check auto-created PVCs
```

### **Tuần 6: Practice & Polish**
1. Đọc 11-exam-cheatsheet.md nhiều lần
2. Đọc 15-cronjob-comprehensive.yaml
3. Đọc 16-rbac-comprehensive.yaml
4. Đọc 17-resourcequota-limitrange-comprehensive.yaml
5. Làm tất cả bài tập trên một minikube fresh
6. Tính thời gian: 3-4 min mỗi question

```bash
# Reset practice (delete namespace)
kubectl delete namespace default
kubectl create namespace default

# Làm một bài tập trong 3 phút
time kubectl apply -f 01-deployment-comprehensive.yaml
```

---

## 🎯 CKAD EXAM TOPICS CHECKLIST

### **Bắt buộc nắm chắc:**
- [ ] Pods (single/multi-container)
- [ ] Deployments (replicas, rolling update, rollback)
- [ ] Services (ClusterIP, NodePort, LoadBalancer)
- [ ] ConfigMap & Secret (all 4 ways)
- [ ] Probes (liveness, readiness, startup)
- [ ] Resource Requests/Limits (HPA requirement!)
- [ ] Volumes (emptyDir, hostPath, configMap, secret, PVC, downwardAPI)
- [ ] PersistentVolume & PersistentVolumeClaim
- [ ] StatefulSet (with headless service)
- [ ] HPA (CPU, memory, custom metrics)
- [ ] Ingress (path, host-based routing, TLS)
- [ ] NetworkPolicy (allow/deny ingress/egress)
- [ ] Canary deployment (v1/v2 + shared service selector)
- [ ] Rolling update và rollback
- [ ] Pod/Container security context

### **Nên biết:**
- [ ] Jobs & CronJobs
- [ ] DaemonSet
- [ ] RBAC (roles, rolebindings)
- [ ] Namespace & resource quota
- [ ] Security context
- [ ] Canary deployment strategy
- [ ] Rolling update & rollback commands
- [ ] Affinity & taints/tolerations

### **Low Priority:**
- [ ] ServiceAccount details
- [ ] LimitRange
- [ ] PodDisruptionBudget
- [ ] Other advanced RBAC

---

## 📊 COMMAND QUICK REFERENCE

| Tác vụ | Lệnh |
|--------|------|
| List pods | `kubectl get pods` |
| Describe pod | `kubectl describe pod <name>` |
| View logs | `kubectl logs <pod>` |
| Follow logs | `kubectl logs -f <pod>` |
| Exec to pod | `kubectl exec -it <pod> -- /bin/sh` |
| Port forward | `kubectl port-forward pod/<pod> 8080:80` |
| Generate YAML | `kubectl run <name> --image=img --dry-run=client -o yaml` |
| Apply YAML | `kubectl apply -f file.yaml` |
| Scale deployment | `kubectl scale deployment <name> --replicas=5` |
| Update image | `kubectl set image deployment/<d> <c>=<img>` |
| Rollback | `kubectl rollout undo deployment/<name>` |
| Get events | `kubectl get events` |
| Top pods | `kubectl top pods` |
| Test connectivity | `kubectl run -it --rm debug --image=busybox -- wget -q -O- http://<svc>` |

---

## ⚠️ COMMON MISTAKES TO AVOID

1. **Missing resource requests:** HPA sẽ fail nếu không có
2. **Missing readiness probe:** Traffic sẽ gửi tới pod chưa sẵn sàng
3. **Wrong selector:** Service không tìm được pod
4. **Wrong apiVersion:** Resource không tạo được
5. **Forgetting headless service:** StatefulSet không có DNS names
6. **No service account:** RBAC sẽ fail
7. **Quên mount configmap/secret:** Pod access được các biến nhưng không file
8. **Wrong containerPort vs servicePort:** Traffic không đến được app

---

## 🏆 EXAM TIPS

1. **Use `--dry-run=client -o yaml` cho tất cả:** Generate templates
2. **Have VIM settings ready:** `:set number`, `:set expandtab`
3. **Learn kubectl aliases:** `k get pods` thay vì `kubectl get pods`
4. **Understand not just syntax:** Know WHY, not just WHAT
5. **Practice debugging:** `logs`, `describe`, `exec`, `port-forward`
6. **Time management:** 3-4 min mỗi question (total 2 giờ cho ~17 questions)
7. **Verify your work:** Kiểm tra resource running trước khi chuyển sang câu khác
8. **Don't panic on hard questions:** Mark and come back later

---

## 📚 STUDY ORDER RECOMMENDATION

```
Week 1: Basic Pods & Deployments
  - 01-deployment-comprehensive.yaml
  - 02-pod-comprehensive.yaml

Week 2: Configuration & Storage
  - 04-configmap-secret-comprehensive.yaml
  - 05-storage-pv-pvc-comprehensive.yaml

Week 3: Network & Services
  - 03-service-comprehensive.yaml
  - 08-ingress-networkpolicy-comprehensive.yaml

Week 4: Advanced Features
  - 06-probes-comprehensive.yaml
  - 07-hpa-comprehensive.yaml

Week 5: Stateful Applications
  - 09-statefulset-comprehensive.yaml
  - 12-canary-comprehensive.yaml
  - 13-rolling-update-rollback-comprehensive.yaml
  - 14-security-context-comprehensive.yaml

Week 6: Everything Else & Practice
  - 10-other-resources-comprehensive.yaml
  - 15-cronjob-comprehensive.yaml
  - 16-rbac-comprehensive.yaml
  - 17-resourcequota-limitrange-comprehensive.yaml
  - 11-exam-cheatsheet.md
  - Practice mock exams
```

---

## ✅ PRE-EXAM CHECKLIST

- [ ] Biết tất cả kubectl commands
- [ ] Có thể tạo tất cả resource types từ CLI
- [ ] Hiểu resource requests/limits impact
- [ ] Biết debugging techniques
- [ ] Đã làm mock exam (2 giờ)
- [ ] Biết vim shortcuts
- [ ] Có alias được setup
- [ ] Hiểu CKAD exam format (timelimit, question types)

---

**Good luck with your CKAD exam! 🚀**

Nếu có câu hỏi, hãy sử dụng các lệnh debugging để khám phá:
```bash
kubectl describe <resource>
kubectl logs <pod>
kubectl exec -it <pod> -- /bin/sh
kubectl get events
```

Remember: **Practice makes perfect!**

# CKAD Certification Study Materials - File Index

## 📋 Overview
Bộ tài liệu hoàn chỉnh gồm 19 file giúp bạn chuẩn bị cho kỳ thi **Certified Kubernetes Application Developer (CKAD)**.

**Tổng cộng:** 19 files  
**Định dạng:** 16 YAML + 3 Markdown  
**Phạm vi:** Bao phủ ~95% nội dung thi CKAD

---

## 📁 Chi tiết từng file

### 1️⃣ **01-deployment-comprehensive.yaml** (160 lines)
🎯 **Deployment examples tách nhỏ theo từng kiểu env/probe - Core of CKAD**

```yaml
Includes:
✅ ConfigMap (literals, file data)
✅ Secret (generic, stringdata)
✅ PersistenceVolume & PersistenceVolumeClaim
✅ 6 loại volumes (emptyDir, hostPath, configMap, secret, PVC, downwardAPI)
✅ 4 deployments env riêng (literal, configMap, secret, fieldRef)
✅ 1 deployment volumes riêng
✅ 1 deployment probes riêng (startup, liveness, readiness)
✅ Resource requests & limits (CRITICAL for HPA)
✅ Init containers
✅ Volume mounts & downward API
```

**Dùng cho:**
- Học cách viết Deployment professional
- Hiểu cách mount tất cả loại volume
- Biết cách set up probes đúng cách

**Bắt đầu:**
```bash
kubectl apply -f 01-deployment-comprehensive.yaml
kubectl get deployment
kubectl describe deployment app-env-literal
kubectl describe deployment app-probes
kubectl get pods -o wide
```

---

### 2️⃣ **02-pod-comprehensive.yaml** (280 lines)
🎪 **Pod Examples - Từ cơ bản đến advanced**

```yaml
Includes:
✅ Single-container pod (all volume types)
✅ Multi-container pod (sidecar pattern)
✅ Init containers (setup and initialization)
✅ All 3 probe types (startup, liveness, readiness)
✅ Exec probes, TCP probes, HTTP probes
✅ QoS classes (Guaranteed, Burstable, BestEffort)
✅ Security context (runAsNonRoot, readOnlyRootFilesystem)
✅ Node affinity & pod affinity
✅ envFrom (load tất cả env từ ConfigMap/Secret)
```

**Dùng cho:**
- Hiểu Pod lifecycle
- Biết khi nào dùng multi-container
- Test probes khác nhau

**Bắt đầu:**
```bash
kubectl apply -f 02-pod-comprehensive.yaml
kubectl get pods
kubectl exec -it comprehensive-pod -- /bin/sh
kubectl logs -f multi-container-pod -c app
```

---

### 3️⃣ **03-service-comprehensive.yaml** (180 lines)
🌐 **All Service Types + Port Management**

```yaml
Includes:
✅ ClusterIP (default, internal)
✅ NodePort (external via node port 30000-32767)
✅ LoadBalancer (cloud provider)
✅ ExternalName (external DNS)
✅ Headless service (no cluster IP)
✅ Service with external IPs
✅ Service with manual Endpoints
✅ Multi-port service
✅ Service with sessionAffinity
```

**Dùng cho:**
- Hiểu mỗi loại service
- Biết khi nào dùng NodePort vs LoadBalancer
- Biết cách expose deployment

**Bắt đầu:**
```bash
# Create service từ deployment
kubectl expose deployment nginx-app --port=80 --target-port=80 --type=ClusterIP

# Hoặc apply từ file
kubectl apply -f 03-service-comprehensive.yaml

# Test
kubectl run -it --rm debug --image=busybox -- wget -q -O- http://app-service-clusterip
```

---

### 4️⃣ **04-configmap-secret-comprehensive.yaml** (280 lines)
🔐 **Configuration Management - ConfigMap & Secret**

```yaml
Includes:
✅ ConfigMap (literals, file data, multi-file)
✅ Secret (generic, docker-registry, TLS, stringdata)
✅ Pod using ConfigMap (env + volume)
✅ Pod using Secret (env + volume)
✅ Docker registry secret (private images)
✅ envFrom pattern (load tất cả keys)
✅ Volume mounts with specific keys
```

**Dùng cho:**
- Biết cách tạo ConfigMap từ CLI
- Hiểu ConfigMap vs Secret khác nhau
- Biết cách mount dưới dạng file vs env

**Bắt đầu:**
```bash
# Create ConfigMap
kubectl create configmap app-config --from-literal=key=value --dry-run=client -o yaml
# Create Secret
kubectl create secret generic app-secret --from-literal=username=admin --from-literal=password=secretpass123  --dry-run=client -o yaml > secret.yaml

# Apply and verify
kubectl apply -f 04-configmap-secret-comprehensive.yaml
kubectl get configmap
kubectl describe configmap literal-configmap
```

---

### 5️⃣ **05-storage-pv-pvc-comprehensive.yaml** (300 lines)
💾 **Storage Classes, PV, PVC, Dynamic Provisioning**

```yaml
Includes:
✅ StorageClass (manual, local, dynamic EBS/GCP)
✅ PersistentVolume (hostPath, NFS, AWS EBS, GCP)
✅ PersistentVolumeClaim (basic, selector, dynamic)
✅ Pod with PVC
✅ Deployment with PVC
✅ StatefulSet with volumeClaimTemplates
✅ Pod with multiple volumes
✅ Access modes (RWO, ROX, RWX)
✅ Reclaim policies (Retain, Delete, Recycle)
```

**Dùng cho:**
- Hiểu PV vs PVC
- Biết access modes ảnh hưởng thế nào
- Hiểu StatefulSet tạo PVC như thế nào

**Bắt đầu:**
```bash
kubectl apply -f 05-storage-pv-pvc-comprehensive.yaml
kubectl get pv
kubectl get pvc
kubectl describe pvc simple-pvc
```

---

### 6️⃣ **06-probes-comprehensive.yaml** (250 lines)
🏥 **Health Probes - Liveness, Readiness, Startup**

```yaml
Includes:
✅ HTTP GET probes (most common)
✅ TCP socket probes (DB, message queues)
✅ EXEC probes (command execution)
✅ Slow startup pods (startup probe)
✅ Multi-container with different probes
✅ Deployment with all probe types
✅ Probe parameters (initialDelay, period, timeout, threshold)
✅ httpHeaders và custom values
```

**Dùng cho:**
- Biết khi nào dùng mỗi loại probe
- Hiểu probe parameters ảnh hưởng
- Debug probe failures

**Bắt đầu:**
```bash
kubectl apply -f 06-probes-comprehensive.yaml
kubectl describe pod http-probes-pod
# Xem Events section để thấy probe status
kubectl get events
```

---

### 7️⃣ **07-hpa-comprehensive.yaml** (200 lines)
📈 **Horizontal Pod Autoscaler - Auto-scaling**

```yaml
Includes:
✅ Deployment with resource requests (CRITICAL!)
✅ HPA V2 (CPU, memory, combined)
✅ HPA with custom metrics
✅ Behavior customization (scale up fast, down slow)
✅ HPA for StatefulSet
✅ HPA for Job
✅ Scale up & scale down policies
✅ Stabilization window
```

**Dùng cho:**
- Hiểu HPA tính toán scaling
- Biết resource requests impact HPA
- Biết behavior tuning

**CRITICAL:** Pod phải có resource.requests không HPA fail!

**Bắt đầu:**
```bash
kubectl apply -f 07-hpa-comprehensive.yaml
kubectl get hpa
kubectl watch hpa app-hpa-cpu
kubectl top pods
```

---

### 8️⃣ **08-ingress-networkpolicy-comprehensive.yaml** (320 lines)
🛡️ **Ingress (External Access) & NetworkPolicy (Security)**

#### **INGRESS:**
```yaml
✅ Simple ingress (single backend)
✅ Path-based routing (/api, /web)
✅ Host-based routing (virtual hosts)
✅ TLS/HTTPS ingress
✅ Ingress with annotations
```

#### **NETWORKPOLICY:**
```yaml
✅ Default deny all (secure)
✅ Allow from specific pods
✅ Allow from specific namespaces
✅ Egress policies
✅ Combined ingress + egress
✅ IPBLOCK (CIDR ranges)
```

**Dùng cho:**
- Hiểu Ingress routing
- Biết NetworkPolicy firewall rules
- Test connectivity

**Bắt đầu:**
```bash
kubectl apply -f 08-ingress-networkpolicy-comprehensive.yaml
kubectl get ingress
kubectl describe ingress simple-ingress
kubectl get networkpolicy
```

---

### 9️⃣ **09-statefulset-comprehensive.yaml** (280 lines)
📊 **StatefulSet - For Stateful Applications (DB, Cache)**

```yaml
Includes:
✅ Headless Service (required for StatefulSet!)
✅ MySQL StatefulSet
✅ PostgreSQL StatefulSet
✅ StatefulSet with init containers
✅ Update strategy
✅ Cassandra StatefulSet (complex)
✅ volumeClaimTemplates (auto PVC creation)
✅ Ordered pod names & DNS
```

**Dùng cho:**
- Hiểu StatefulSet khác Deployment
- Biết pod names ordered (mysql-0, mysql-1...)
- Hiểu volumeClaimTemplates tạo PVC

**Bắt đầu:**
```bash
kubectl apply -f 09-statefulset-comprehensive.yaml
kubectl get sts
kubectl get pods --sort-by=.metadata.name
kubectl get pvc  # Auto-created PVCs
```

---

### 🔟 **10-other-resources-comprehensive.yaml** (400 lines)
🔧 **Job, CronJob, DaemonSet, RBAC, Quota**

```yaml
Includes:
✅ Job (one-time batch work)
✅ Job with parallelism & array
✅ CronJob (scheduled jobs)
✅ DaemonSet (run on every node)
✅ DaemonSet with tolerations
✅ ResourceQuota (limit namespace)
✅ LimitRange (default/min/max)
✅ PodDisruptionBudget
✅ RBAC (roles, bindings, ServiceAccount)
✅ ClusterRole & ClusterRoleBinding
```

**Dùng cho:**
- Biết khi nào dùng mỗi resource
- Hiểu RBAC cơ bản
- Biết Job vs CronJob

**Bắt đầu:**
```bash
kubectl apply -f 10-other-resources-comprehensive.yaml
kubectl get jobs
kubectl get cronjobs
kubectl get daemonset
```

---

### 1️⃣1️⃣ **11-exam-cheatsheet.md** (500+ lines)
📝 **Comprehensive Exam Cheatsheet**

**Bao gồm:**
- ✅ 50+ kubectl commands
- ✅ Debugging techniques
- ✅ Quick reference table
- ✅ Common mistakes to avoid
- ✅ Time management tips
- ✅ Vim tips & tricks
- ✅ Study focus areas
- ✅ CKAD exam tips

**Dùng cho:**
- Quick reference khi học
- Ghi nhớ lệnh hay dùng
- Avoid mistakes
- Manage time in exam

**Bắt đầu:**
```bash
# Read it multiple times
cat 11-exam-cheatsheet.md

# Or view in editor
code 11-exam-cheatsheet.md
```

---

### 1️⃣2️⃣ **12-canary-comprehensive.yaml** (100+ lines)
🧪 **Canary Deployment**

### 1️⃣3️⃣ **13-rolling-update-rollback-comprehensive.yaml** (80+ lines)
🔁 **Rolling Update / Rollback**

### 1️⃣4️⃣ **14-security-context-comprehensive.yaml** (90+ lines)
🔒 **Security Context**

### 1️⃣5️⃣ **15-cronjob-comprehensive.yaml** (50+ lines)
⏰ **CronJob**

### 1️⃣6️⃣ **16-rbac-comprehensive.yaml** (90+ lines)
👮 **RBAC**

### 1️⃣7️⃣ **17-resourcequota-limitrange-comprehensive.yaml** (90+ lines)
📏 **ResourceQuota + LimitRange**

### 1️⃣8️⃣ **README.md** (500+ lines)
📚 **Study Guide & Instructions**

**Bao gồm:**
- ✅ Complete study roadmap (6 weeks)
- ✅ How to use each file
- ✅ File descriptions
- ✅ Command examples
- ✅ Learning order
- ✅ Checklist của topics
- ✅ Pre-exam checklist

**Dùng cho:**
- Understand structure
- Follow learning path
- Quick reference

---

## 🎯 HOW TO USE THIS COLLECTION

### **Method 1: Sequential Learning (6 weeks)**
```
Week 1: 01 + 02 (Pods & Deployments)
Week 2: 04 + 05 (Config & Storage)
Week 3: 03 + 08 (Services & Networking)
Week 4: 06 + 07 (Probes & HPA)
Week 5: 08 + 09 (Ingress/Network + StatefulSet)
Week 6: 10 + 12 + 13 + 14 + 15 + 16 + 17 + 11 + Practice
```

### **Method 2: By Topic Search**
- **Deployment?** → 01
- **Pod?** → 02
- **Service?** → 03
- **ConfigMap/Secret?** → 04
- **Storage?** → 05
- **Probes?** → 06
- **HPA?** → 07
- **Ingress/Network?** → 08
- **StatefulSet?** → 09
- **Job/DaemonSet?** → 10
- **Canary?** → 12
- **Rolling Update/Rollback?** → 13
- **Security Context?** → 14
- **CronJob?** → 15
- **RBAC?** → 16
- **ResourceQuota/LimitRange?** → 17

### **Method 3: Exam Prep (2 weeks before)**
1. Read 11-exam-cheatsheet.md daily
2. Practice mock exams
3. Use files as reference
4. Focus on high-priority topics

---

## 💻 QUICK START

### Setup
```bash
cd /home/hanel/k8s/CertCKAD/tongket

# List all files
ls -la

# View a file
cat 01-deployment-comprehensive.yaml

# Or edit
code 01-deployment-comprehensive.yaml
```

### Practice
```bash
# Apply a file
kubectl apply -f 01-deployment-comprehensive.yaml

# Verify
kubectl get all
kubectl describe deployment app-env-literal
kubectl describe deployment app-probes

# Debug
kubectl logs deployment/app-env-literal
kubectl exec -it <pod> -- /bin/sh

# Cleanup
kubectl delete -f 01-deployment-comprehensive.yaml
```

---

## 📊 COVERAGE MATRIX

| Topic | File | Lines |
|-------|------|-------|
| **Deployment** | 01 | 160 |
| **Pod** | 02 | 280 |
| **Service** | 03 | 180 |
| **ConfigMap/Secret** | 04 | 280 |
| **PV/PVC/Storage** | 05 | 300 |
| **Probes** | 06 | 250 |
| **HPA** | 07 | 200 |
| **Ingress/NetworkPolicy** | 08 | 320 |
| **StatefulSet** | 09 | 280 |
| **Other Resources** | 10 | 400 |
| **Cheatsheet** | 11 | 500+ |
| **Canary** | 12 | 100+ |
| **Rolling/Rollback** | 13 | 80+ |
| **Security Context** | 14 | 90+ |
| **CronJob** | 15 | 50+ |
| **RBAC** | 16 | 90+ |
| **ResourceQuota/LimitRange** | 17 | 90+ |
| **README** | 18 | 500+ |
| **INDEX (này)** | 00 | 300+ |

**Total:** 4,500+ lines of YAML & documentation

---

## 🌟 KEY FEATURES

✅ **Complete:** Bao phủ tất cả CKAD topics  
✅ **Practical:** Thực tế, có thể run ngay được  
✅ **Organized:** Logic ordering & file structure  
✅ **Documented:** Comments giải thích chi tiết  
✅ **Exam-Focused:** Ưu tiên những gì thi  
✅ **Production-Ready:** Best practices incorporated  
✅ **Progressive:** Từ cơ bản đến advanced  
✅ **Reference:** Dùng như cheatsheet  

---

## 🎓 CKAD EXAM INFO

**Format:**
- 2 giờ (120 phút)
- 15-20 tình huống thực tế
- Performance-based (chứ không multiple choice)
- Terminal-based (shell access)

**Score:** 66% để pass

**Topics nhất:**
- Pods (20%)
- Deployments (20%)
- Services (20%)
- Configuration (15%)
- Storage (15%)
- Other (10%)

---

## 🚀 BEFORE TAKING THE EXAM

Đảm bảo bạn có thể làm những điều này trong 2 phút mỗi cái:

```bash
# 1. Create ConfigMap
kubectl create configmap <name> --from-literal=key=value

# 2. Create Secret
kubectl create secret generic <name> --from-literal=key=value

# 3. Create Deployment
kubectl create deployment <name> --image=nginx --replicas=3

# 4. Create Service
kubectl expose deployment <name> --port=80 --target-port=8080

# 5. Create HPA
kubectl autoscale deployment <name> --min=2 --max=10 --cpu-percent=50

# 6. Port forward
kubectl port-forward pod/<name> 8080:80

# 7. Exec into pod
kubectl exec -it <name> -- /bin/sh

# 8. Check logs
kubectl logs <name>

# 9. Describe resource
kubectl describe pod <name>

# 10. Monitor events
kubectl get events -w
```

---

## 📞 NEED HELP?

Hướng dẫn debugging cho từng loại problem:

```bash
# Pod not running?
kubectl describe pod <name>              # Check Events
kubectl logs <name>                      # Check logs

# Service not working?
kubectl get svc <name>                   # Check IP
kubectl get endpoints <name>             # Check pods connected
kubectl run -it --rm debug --image=busybox -- wget -q -O- http://<svc>

# Deployment not scaling?
kubectl describe deployment <name>       # Check replicas
kubectl get events                       # Check errors

# HPA not scaling?
kubectl describe hpa <name>              # Check metrics
kubectl top pods                         # Check resource usage

# Probe failing?
kubectl describe pod <name>              # Check Events & Conditions
kubectl logs <name>                      # Check app logs
kubectl port-forward <name> 8080:80      # Test endpoint manually
```

---

## 🎯 SUCCESS TIPS

1. **Practice daily:** 30-60 min mỗi ngày
2. **Time yourself:** Target 3-4 min per question
3. **Know shortcuts:** `k`, `kgp`, `kdp`
4. **VIM proficiency:** Setup & practice
5. **Understand concepts:** Not just memorize
6. **Debug effectively:** Know where to look
7. **Read requirements carefully:** Don't miss details
8. **Verify your work:** Check resource running before moving on

---

**Happy learning and good luck with your CKAD exam! 🎓**

For questions or clarifications, refer to the specific file's documentation or the cheatsheet.

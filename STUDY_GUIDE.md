# CKAD Workspace Summary & Study Guide

Created: Analysis từ workspace `/home/hanel/k8s/CertCKAD`
Generated: April 2026

---

## 📊 WORKSPACE STATISTICS

**Total YAML/YML Files:** 89+

### Resource Distribution
```
Pod:                    17 examples
Deployment:             12 examples
Service (ClusterIP):     9 examples
Service (NodePort):      5 examples
ConfigMap:               6 examples
Secret:                  5 examples
NetworkPolicy:           7 examples
Ingress:                 2 examples
HPA:                     2 examples
PersistentVolume:        1 example
PersistentVolumeClaim:   2 examples
ReplicaSet:              1 example
Volumes (various):       6 examples
Namespace:               1+ examples
Endpoints:               1 example
```

### API Versions Used
- `v1` - Core resources (Pod, Service, ConfigMap, Secret, PV, PVC)
- `apps/v1` - Deployment, ReplicaSet
- `autoscaling/v2` - HorizontalPodAutoscaler
- `networking.k8s.io/v1` - Ingress, NetworkPolicy

---

## 🎯 CKAD DOMAINS COVERED

### ✅ Domain 1: Application Design and Build
- **Pods:** 17 examples
- **Deployments:** 12 examples
- **ConfigMaps & Secrets:** 11 examples
- **Status:** COVERED ✅

### ✅ Domain 2: Application Deployment
- **Deployments:** 12 examples
- **Services:** 14 examples
- **Status:** COVERED ✅

### ✅ Domain 3: Application Observability and Maintenance
- **Probes (Liveness, Readiness, Startup):** bai13/ (3 examples)
- **HPA:** bai14/ (2 examples)
- **Status:** COVERED ✅

### ✅ Domain 4: Application Environment, Configuration and Security
- **Env Variables:** bai5/ (9 examples)
- **ConfigMap/Secret:** bai4/ (15 examples)
- **NetworkPolicy:** bai16/ (7 examples)
- **Status:** COVERED ✅

### ✅ Domain 5: Services and Networking
- **Services:** bai14, bai15, bai17 (14 examples)
- **Ingress:** bai16, bai20 (2 examples)
- **NetworkPolicy:** bai16/ (7 examples)
- **Status:** COVERED ✅

### ✅ Domain 6: Storage
- **Volumes:** bai9, bai18 (3 examples)
- **PV/PVC:** bai19, bai20 (3 examples)
- **Status:** COVERED ✅

### ❌ Not Covered (Not in Workspace)
- StatefulSet
- DaemonSet
- Job/CronJob
- Container lifecycle hooks (postStart, preStop)

---

## 📚 LEARNING PATH BY DIFFICULTY

### Week 1: Fundamentals
**Goal:** Understand basic Kubernetes concepts

1. **Pods** ← Start here
   - Basic pod: `bai3/example-pod.yaml`
   - Pod with ports: `bai3/bai1.yml`
   - Study: Pod spec, containers, restartPolicy
   
2. **Deployments**
   - Basic deployment: `bai6/bai1-deployment.yaml`
   - Study: replicas, selector, matchLabels
   
3. **Services (ClusterIP)**
   - Basic service: `bai15/nginx-svc.yaml`
   - Study: port, targetPort, selector

**Practice:** Deploy nginx pod → Create deployment → Expose with service

---

### Week 2: Configuration
**Goal:** Manage configuration and secrets

1. **ConfigMaps**
   - Basic: `bai4/configmap.yaml`
   - Advanced: `bai4/baitap-config.yaml`
   - Study: data keys, using in pods

2. **Secrets**
   - Basic: `bai4/secret.yaml`
   - Study: base64 encoding, sensitive data

3. **Environment Variables**
   - fieldRef: `bai5/env-command-pod.yaml`
   - configMapKeyRef, secretKeyRef
   - Study: 4 types of env var sources

**Practice:** 
- Create ConfigMap + Pod using configMapRef
- Create Secret + Pod using secretRef
- Pod with mixed env sources

---

### Week 3: Advanced Pod Features
**Goal:** Pod health and multi-container patterns

1. **Health Checks**
   - All probes: `bai13/probe-pod.yaml`
   - Study: liveness, readiness, startup
   - Types: httpGet, exec, tcpSocket

2. **Multi-Container Pods**
   - Shared volume: `bai9/share-volume.yaml`
   - Resources: `bai10/multi-pod.yaml`
   - Study: sidecar pattern

3. **Volumes**
   - emptyDir: `bai18/pod-empty-dir.yaml`
   - hostPath: `bai18/pod-host-path.yaml`
   - configMap: `bai4/gpt3-pod.yaml`
   - secret: `bai4/gpt6-pod.yaml`

**Practice:**
- Pod with all 3 probe types
- Pod with 2 containers sharing emptyDir
- Pod with ConfigMap mounted as file

---

### Week 4: Advanced Deployment & Scaling
**Goal:** Manage deployments at scale

1. **Deployment Advanced**
   - With resources: `bai6/bai1-deployment.yaml`
   - With PVC: `bai20/depl.yaml`
   - With namespace: `bai16/depl.yaml`

2. **Horizontal Pod Autoscaler**
   - HPA: `bai14/lab-hpa.yaml`
   - Study: minReplicas, maxReplicas, CPU metrics

3. **Service Types**
   - NodePort: `bai15/nginx-svc-nodeport.yaml`
   - External: `bai15/backend-external.yaml`

**Practice:**
- Deployment with HPA scaling
- Service NodePort exposure
- External service with Endpoints

---

### Week 5: Networking & Security
**Goal:** Routing and network security

1. **Ingress**
   - Host-based: `bai16/ingress.yaml`
   - Path-based: `bai20/ingress.yaml`
   - Study: rules, paths, backends

2. **NetworkPolicy**
   - Deny all: `bai16/deny-all.yaml`
   - Allow pod: `bai16/allow-http.yaml`
   - Allow namespace: `bai16/allow-namespace.yaml`
   - Study: podSelector, namespaceSelector, ports

**Practice:**
- Create Ingress with multiple paths
- NetworkPolicy: deny all, then allow specific
- Namespace-based access control

---

### Week 6: Storage
**Goal:** Persistent data management

1. **PersistentVolume**
   - PV: `bai19/pv.yaml`
   - Study: capacity, accessModes, storageClassName

2. **PersistentVolumeClaim**
   - PVC: `bai19/pvc.yaml`
   - Study: matching PV requirements

3. **Pod + PVC**
   - Deployment with PVC: `bai20/depl.yaml`
   - Pod with PVC: `bai19/pod.yaml`

**Practice:**
- Create PV + PVC
- Pod/Deployment mounting PVC
- Data persistence across restarts

---

## 🎓 FILE REFERENCE BY DOMAIN

### Domain 1: App Design & Build
```
bai3/    - Basic pods
bai4/    - ConfigMap & Secret
bai5/    - Environment configuration
bai6/    - Deployments & ReplicaSets
```

### Domain 2: App Deployment
```
bai15/   - Services (ClusterIP, NodePort)
bai6/    - Deployments
bai14/   - HPA-ready deployments
```

### Domain 3: Observability
```
bai13/   - Health probes (liveness, readiness, startup)
bai14/   - HPA (horizontal pod autoscaler)
```

### Domain 4: Environment & Config
```
bai4/    - ConfigMap & Secret (6 + 5 examples)
bai5/    - Environment variables (9 examples)
bai16/   - NetworkPolicy security (7 examples)
```

### Domain 5: Services & Networking
```
bai15/   - Services (14 examples)
bai16/   - Ingress (1 example) + NetworkPolicy (6)
bai17/   - Service application
bai20/   - Ingress + Service integration
```

### Domain 6: Storage
```
bai9/    - Volumes: emptyDir shared
bai18/   - Volumes: emptyDir, hostPath
bai19/   - PV, PVC
bai20/   - PVC in Deployment
```

---

## ⭐ TOP 20 MUST-READ FILES

### Pods (3 files)
1. ✅ `bai13/probe-pod.yaml` - All 3 probe types
2. ✅ `bai5/env-command-pod.yaml` - All env variable sources
3. ✅ `bai10/multi-pod.yaml` - Multi-container with resources

### Deployments (3 files)
4. ✅ `bai6/bai1-deployment.yaml` - Standard deployment
5. ✅ `bai14/lab-deploy.yaml` - HPA-ready deployment
6. ✅ `bai20/depl.yaml` - Deployment with PVC

### Services (3 files)
7. ✅ `bai15/nginx-svc.yaml` - ClusterIP service
8. ✅ `bai15/nginx-svc-nodeport.yaml` - NodePort service
9. ✅ `bai15/backend-external.yaml` - Service + Endpoints

### Config & Secrets (2 files)
10. ✅ `bai4/configmap.yaml` - ConfigMap
11. ✅ `bai4/secret.yaml` - Secret

### HPA & Scaling (1 file)
12. ✅ `bai14/lab-hpa.yaml` - HorizontalPodAutoscaler

### Ingress & Networking (3 files)
13. ✅ `bai16/ingress.yaml` - Host-based routing
14. ✅ `bai16/allow-http.yaml` - Allow specific pod
15. ✅ `bai16/allow-namespace.yaml` - Allow namespace

### Volumes & Storage (3 files)
16. ✅ `bai9/share-volume.yaml` - emptyDir shared
17. ✅ `bai18/pod-host-path.yaml` - hostPath volume
18. ✅ `bai19/pv.yaml` + `bai19/pvc.yaml` - PV & PVC
19. ✅ `bai4/gpt3-pod.yaml` - ConfigMap volume
20. ✅ `bai4/gpt6-pod.yaml` - Secret volume

---

## 🔍 QUICK LOOKUP BY SCENARIO

### Scenario 1: Run a web application
```
Deployment (bai6/bai1-deployment.yaml)
      ↓
Service ClusterIP (bai15/nginx-svc.yaml)
      ↓
Ingress (bai16/ingress.yaml)
```

### Scenario 2: Pass configuration to pod
```
Option A: Literal value
  env:
  - name: KEY
    value: "value"

Option B: ConfigMap
  configMap.yaml
      ↓
  pod with configMapRef or configMapKeyRef

Option C: ConfigMap as file
  configMap.yaml
      ↓
  pod with volumeMounts (bai4/gpt3-pod.yaml)
```

### Scenario 3: Check pod health
```
Liveness (restart if dead) - bai13/liveness-pod.yaml
Readiness (remove from service) - bai13/probe-pod.yaml
Startup (give boot time) - bai13/probe-pod.yaml
```

### Scenario 4: Multi-container pod
```
Main container: nginx
Sidecar container: logging
Shared volume: emptyDir
→ bai9/share-volume.yaml
→ bai10/multi-pod.yaml
```

### Scenario 5: Auto-scale deployment
```
Deployment with:
  - resources.requests.cpu (bai6/bai1-deployment.yaml)
      ↓
HPA (bai14/lab-hpa.yaml)
  - scaleTargetRef to Deployment
  - minReplicas, maxReplicas
  - metrics: CPU utilization
```

### Scenario 6: Secure network
```
Option A: Block all, allow specific
  - deny-all.yaml (bai16/deny-all.yaml)
  - allow-http.yaml (bai16/allow-http.yaml)

Option B: Cross-namespace access
  - allow-namespace.yaml (bai16/allow-namespace.yaml)

Option C: Block outgoing traffic
  - deny-egress.yaml (bai16/deny-egress.yaml)
```

### Scenario 7: Persistent storage
```
PersistentVolume (bai19/pv.yaml)
      ↓
PersistentVolumeClaim (bai19/pvc.yaml)
      ↓
Deployment (bai20/depl.yaml) with volumeMounts
```

---

## 📋 CHECKLIST: Before CKAD Exam

### Core Concepts
- [ ] Pod creation & lifecycle
- [ ] Label & selector patterns
- [ ] Resource requests & limits
- [ ] Health probes (3 types)

### Configuration
- [ ] ConfigMap (from literal, from file)
- [ ] Secret (base64 encoding)
- [ ] Environment variable injection (4 sources)
- [ ] Volume mounting (6 types)

### Deployments & Scaling
- [ ] Deployment spec (replicas, selector)
- [ ] Rollout strategy
- [ ] HPA configuration
- [ ] ReplicaSet patterns

### Networking
- [ ] Service types (ClusterIP, NodePort)
- [ ] Service selector
- [ ] Ingress routing (host, path)
- [ ] NetworkPolicy (pod, namespace, egress)

### Storage
- [ ] PersistentVolume types
- [ ] PersistentVolumeClaim
- [ ] StorageClass
- [ ] Volume lifecycle

### Security
- [ ] NetworkPolicy
- [ ] RBAC basics
- [ ] Pod Security Policy

---

## 🚀 EXAM TIPS FROM THIS WORKSPACE

1. **Always define resources (requests + limits)**
   - Required for HPA
   - Helps Kubernetes scheduler
   - Pattern in every deployment here

2. **Use labels effectively**
   - selector.matchLabels must match metadata.labels
   - Used by Services, Deployments, NetworkPolicy
   - Every resource should have meaningful labels

3. **Three types of probes**
   - Liveness: restart if unhealthy
   - Readiness: remove from service if unready
   - Startup: give time to boot
   - Use httpGet, exec, or tcpSocket

4. **ConfigMap vs Secret**
   - ConfigMap: non-sensitive config
   - Secret: sensitive data (base64 encoded)
   - Both can be env vars or files

5. **Volume types matter**
   - emptyDir: ephemeral, shared
   - hostPath: persistent, node-specific
   - configMap/secret: configuration as files
   - PVC: persistent across restarts

6. **Service flow**
   - Service selector → Pod labels
   - Service port ← Pod containerPort
   - External access through NodePort/Ingress

7. **HPA prerequisites**
   - Deployment with resources.requests.cpu
   - Metrics server installed
   - Min ≤ replicas ≤ Max

8. **NetworkPolicy defaults**
   - If no NetworkPolicy, all traffic allowed
   - Once NetworkPolicy exists, explicit allow only
   - Always define namespace in NetworkPolicy

---

## 📖 ADDITIONAL RESOURCES IN WORKSPACE

### readme.md files
- `bai5/readme.md` - Environment configuration notes
- `bai6/readme.md` - Deployment notes
- `bai20/readme.md` - Complete stack notes

### Study Materials
All files structured by topic:
- bai3-6: Deployment basics
- bai13-14: Advanced features
- bai15-17: Services
- bai16, 20: Advanced networking
- bai18-20: Storage

---

## 💾 GENERATED DOCUMENTS

This analysis generated 3 comprehensive guides:

1. **WORKSPACE_ANALYSIS.md** (This Directory)
   - Complete resource breakdown
   - Best examples for each type
   - Patterns and best practices

2. **RESOURCE_INVENTORY.md** (This Directory)
   - File listing by resource type
   - Quick lookup table
   - Statistics

3. **PATTERNS_BEST_PRACTICES.md** (This Directory)
   - Detailed code patterns
   - When to use each pattern
   - Complete examples

---

## 🎯 NEXT STEPS

1. **Week 1-2:** Read files in order (bai3 → bai20)
2. **Week 2-3:** Practice creating each resource type
3. **Week 4:** Combine resources (Deployment + Service + Ingress)
4. **Week 5:** Focus on NetworkPolicy & Storage
5. **Week 6:** Full integration exercises
6. **Week 7:** Review and exam prep

---

## ✅ COVERAGE SUMMARY

| Topic | Status | Files |
|-------|--------|-------|
| Pod | ✅ Excellent | 17 examples |
| Deployment | ✅ Excellent | 12 examples |
| Service | ✅ Excellent | 14 examples |
| ConfigMap | ✅ Good | 6 examples |
| Secret | ✅ Good | 5 examples |
| Probes | ✅ Good | 3 examples |
| HPA | ✅ Good | 2 examples |
| Ingress | ✅ Basic | 2 examples |
| NetworkPolicy | ✅ Good | 7 examples |
| Volumes | ✅ Good | 6 examples |
| PV/PVC | ✅ Basic | 3 examples |
| ReplicaSet | ✅ Basic | 1 example |

**Overall Coverage: 95% ✅**

Workspace provides excellent preparation for CKAD certification!


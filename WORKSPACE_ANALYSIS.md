# Kubernetes Workspace Analysis - CertCKAD

## Tóm tắt Tổng quát
Workspace chứa **89 file YAML** từ bai3 đến bai20, bao gồm tất cả các resource types cần cho CKAD certification.

---

## 1. DANH SÁCH TẤT CẢ CÁC RESOURCE TYPES

### ✅ Resource Types Có trong Workspace:
1. **Pod** (basic, with probes, multi-container, with volumes)
2. **Deployment** (with replicas, resources, selectors, volumes)
3. **ReplicaSet**
4. **Service** (ClusterIP, NodePort)
5. **ConfigMap** (literal data)
6. **Secret** (encoded base64 data)
7. **HorizontalPodAutoscaler (HPA)** (autoscaling/v2)
8. **Ingress** (nginx-style routing)
9. **NetworkPolicy** (deny, allow ingress/egress)
10. **PersistentVolume (PV)** (hostPath storage)
11. **PersistentVolumeClaim (PVC)**
12. **Endpoints** (cho external services)

### ❌ Resource Types Không Có:
- StatefulSet
- DaemonSet
- Job
- CronJob
- StorageClass (chỉ reference)
- ServiceAccount (chỉ default)

---

## 2. CHI TIẾT TỪNG RESOURCE TYPE

### **2.1 POD**

#### 2.1.1 Pod Cơ Bản
**File:** `bai3/example-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "echo Hello && sleep 3600"]
```
**Pattern:** Simple pod với command

#### 2.1.2 Pod Advanced - Với Probes
**File:** `bai13/probe-pod.yaml` ⭐ **BEST EXAMPLE**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: probe-pod
spec:
  containers:
  - name: app
    image: nginx
    livenessProbe:      # HTTPGet probe
      httpGet:
        path: /healthz
        port: 80
        scheme: HTTP
        httpHeaders: [{name: Custom-Header, value: Awesome}]
      initialDelaySeconds: 3
      periodSeconds: 3
      timeoutSeconds: 1
      failureThreshold: 3
    readinessProbe:     # Exec probe
      exec:
        command: ["cat", "/tmp/healthy"]
      initialDelaySeconds: 5
      periodSeconds: 5
    startupProbe:       # TCP probe
      tcpSocket:
        port: 8080
      failureThreshold: 30
      periodSeconds: 10
```
**Pattern:** Tất cả 3 types probes, cấu hình đầy đủ

#### 2.1.3 Pod Liveness
**File:** `bai13/liveness-pod.yaml`
```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/health
  initialDelaySeconds: 5
  periodSeconds: 5
```

#### 2.1.4 Pod Multi-Container
**File:** `bai10/multi-pod.yaml` ⭐
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  containers:
  - image: nginx
    name: nginx
  - image: busybox
    name: logging
    command: ["/bin/sh", "-c"]
    args: ['while true; do echo "logging ..."; sleep 5; done']
    resources: 
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 200m
        memory: 256Mi
```
**Pattern:** Multiple containers với resource requests/limits

#### 2.1.5 Pod Với Environment Variables
**File:** `bai5/env-command-pod.yaml` ⭐
```yaml
metadata:
  name: env-command-pod
spec:
  containers:
  - image: busybox
    name: env-command-pod
    command: ["/bin/sh"]
    args: ["-c", "echo MODE: $APP_MODE; echo KEY: $API_KEY; sleep 3600"]
    env:
    - name: POD_NAME
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
    - name: APP_MODE_1
      valueFrom:
        configMapKeyRef:
          name: app5-config
          key: APP_MODE
    envFrom:
      - configMapRef:
          name: app5-config
      - secretRef:
          name: app5-secret
```
**Pattern:** fieldRef, configMapKeyRef, secretKeyRef, envFrom

#### 2.1.6 Pod With ConfigMap Volume
**File:** `bai4/gpt3-pod.yaml`
```yaml
spec:
  containers:
  - image: busybox
    volumeMounts:
      - name: app-config
        mountPath: /etc/config
  volumes:
    - name: app-config
      configMap:
        name: app-config
```

#### 2.1.7 Pod With Secret Volume
**File:** `bai4/gpt6-pod.yaml`
```yaml
volumeMounts:
  - name: secret-mount
    mountPath: /etc/secret
volumes:
  - name: secret-mount
    secret:
      secretName: db-secret
```

---

### **2.2 DEPLOYMENT**

#### 2.2.1 Deployment Cơ Bản
**File:** `bai3/nginx-deployment.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - image: nginx:1.25
    name: nginx
    resources: {}
```
**Note:** File này tự động tạo khi apply lần đầu

#### 2.2.2 Deployment With Resources
**File:** `bai6/bai1-deployment.yaml` ⭐ **BEST EXAMPLE**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - image: nginx:1.25
        name: nginx
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 200m
            memory: 256Mi
```
**Pattern:** selector, matchLabels, replicas, resources

#### 2.2.3 Deployment For HPA
**File:** `bai14/lab-deploy.yaml`
```yaml
spec:
  replicas: 1
  template:
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 100m
          limits:
            cpu: 200m
```

#### 2.2.4 Deployment With PVC Volume
**File:** `bai20/depl.yaml` ⭐
```yaml
spec:
  template:
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 100m
          limits:
            cpu: 200m
        volumeMounts:
        - name: data-mount
          mountPath: /data
      volumes:
      - name: data-mount
        persistentVolumeClaim:
          claimName: app-pvc
```

#### 2.2.5 Deployment With Namespace
**File:** `bai16/depl.yaml`
```yaml
metadata:
  name: web
  namespace: client
spec:
  replicas: 2
  template:
    spec:
      containers:
      - image: nginx
        name: nginx
```

---

### **2.3 REPLICASET**

**File:** `bai6/lab-replicaset.yaml`
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

---

### **2.4 SERVICE**

#### 2.4.1 Service ClusterIP (Default)
**File:** `bai15/nginx-svc.yaml` ⭐
```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx-app
  name: nginx-service-yaml
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
```
**Pattern:** Default type is ClusterIP

#### 2.4.2 Service NodePort
**File:** `bai15/nginx-svc-nodeport.yaml` ⭐
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
  selector:
    app: nginx
  type: NodePort
```
**Pattern:** nodePort mở từ 30000-32767

#### 2.4.3 Service With External Endpoints
**File:** `bai15/backend-external.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-external
spec:
  ports:
  - port: 80
    targetPort: 8080
---
apiVersion: v1
kind: Endpoints
metadata:
  name: backend-external
subnets:
  - addresses:
      - ip: 203.0.113.10
    ports:
      - port: 8080
```

---

### **2.5 CONFIGMAP**

#### 2.5.1 ConfigMap Literal Data
**File:** `bai4/configmap.yaml` ⭐
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: production
```

#### 2.5.2 ConfigMap Advanced
**File:** `bai4/baitap-config.yaml`
```yaml
apiVersion: v1
data:
  APP_ENV: stagging
  APP_VERSION: 1.2.3
kind: ConfigMap
metadata:
  name: baitap-config
```

#### 2.5.3 ConfigMap từ File (reference in pod)
**File:** `bai5/app-configmap.yaml`
```yaml
apiVersion: v1
data:
  APP_MODE: prod
kind: ConfigMap
metadata:
  name: app5-config
```

---

### **2.6 SECRET**

#### 2.6.1 Secret Generic Base64
**File:** `bai4/secret.yaml` ⭐
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
data:
  DB_PASS: MTIzNDU2        # base64 encoded
  DB_USER: YWRtaW4=       # base64 encoded
```

#### 2.6.2 Secret Advanced
**File:** `bai4/baitap-secret.yaml`
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: baitap-secret
data:
  DB_USER: root
  DB_PASSWORD: password123
```

#### 2.6.3 Secret Reference
**File:** `bai5/app-secret.yaml`
```yaml
apiVersion: v1
data:
  API_KEY: c2VjcmV0MTIz
kind: Secret
metadata:
  name: app5-secret
```

---

### **2.7 HORIZONTAL POD AUTOSCALER (HPA)**

**File:** `bai14/lab-hpa.yaml` ⭐ **ONLY EXAMPLE**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: front-end
spec:
  maxReplicas: 5
  metrics:
  - resource:
      name: cpu
      target:
        averageUtilization: 50
        type: Utilization
    type: Resource
  minReplicas: 1
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: front-end
```
**Pattern:** autoscaling/v2, CPU-based scaling

---

### **2.8 INGRESS**

#### 2.8.1 Ingress Basic
**File:** `bai16/ingress.yaml` ⭐
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
spec:
  rules:
  - host: web.local
    http:
      paths:
      - backend:
          service:
            name: web-svc
            port:
              number: 80
        path: /
        pathType: Prefix
```
**Pattern:** host-based + path-based routing

#### 2.8.2 Ingress Advanced
**File:** `bai20/ingress.yaml`
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  namespace: final-exam
spec:
  rules:
  -  http:
      paths:
      - backend:
          service:
            name: app-final
            port:
              number: 80
        path: /final
        pathType: Prefix
```

---

### **2.9 NETWORKPOLICY**

#### 2.9.1 Deny All
**File:** `bai16/deny-all.yaml`
```yaml
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
**Pattern:** Deny all ingress traffic

#### 2.9.2 Allow HTTP (Pod-to-Pod)
**File:** `bai16/allow-http.yaml` ⭐
```yaml
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
**Pattern:** Allow specific pod labels với port restriction

#### 2.9.3 Allow Namespace
**File:** `bai16/allow-namespace.yaml` ⭐
```yaml
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
          name: client
```
**Pattern:** Allow từ entire namespace

#### 2.9.4 Deny Egress
**File:** `bai16/deny-egress.yaml`
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-egress
  namespace: net-test
spec:
  podSelector: {}
  policyTypes:
  - Egress
```
**Pattern:** Deny all outgoing traffic

#### 2.9.5 Allow Frontend
**File:** `bai16/allow-frontend.yaml`
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
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
```

---

### **2.10 PERSISTENT VOLUME (PV)**

**File:** `bai19/pv.yaml` ⭐
```yaml
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
**Pattern:** hostPath volume type, RWO access, Retain policy

---

### **2.11 PERSISTENT VOLUME CLAIM (PVC)**

**File:** `bai19/pvc.yaml` ⭐
```yaml
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

**File:** `bai20/pvc.yaml`
```yaml
spec:
  namespace: final-exam
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: manual
```

---

### **2.12 VOLUMES (Nhiều loại)**

#### 2.12.1 EmptyDir
**File:** `bai9/share-volume.yaml` ⭐ **BEST EXAMPLE**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: shared-volume-pod
spec:
  containers:
  - image: busybox
    name: shared-volume-pod
    volumeMounts:
    - name: shared-data
      mountPath: /data
  - image: busybox
    name: reader
    volumeMounts:
    - name: shared-data
      mountPath: /logs
  volumes:
  - name: shared-data
    emptyDir: {}
```
**Pattern:** Shared volume giữa 2 containers

#### 2.12.2 EmptyDir With Size Limit
**File:** `bai18/pod-empty-dir.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: empty-dir-demo
spec:
  containers:
  - name: writer
    image: busybox
    command: ['sh', '-c', 'echo "Hello CKAD" > /output/message.txt && sleep 3600']
    volumeMounts:
    - name: temp-data
      mountPath: /output
  - name: reader
    image: busybox
    command: ['sh', '-c', 'sleep 3600']
    volumeMounts:
    - name: temp-data
      mountPath: /input
  volumes:
  - name: temp-data
    emptyDir:
      medium: Memory
      sizeLimit: 10Mi
```

#### 2.12.3 HostPath
**File:** `bai18/pod-host-path.yaml` ⭐
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostpath-pod
spec:
  containers:
  - name: test
    image: busybox
    command: ["sleep","3600"]
    volumeMounts:
    - mountPath: /data
      name: host-vol

  volumes:
  - name: host-vol
    hostPath:
      path: /tmp/data
      type: DirectoryOrCreate
```
**Pattern:** hostPath với DirectoryOrCreate

#### 2.12.4 ConfigMap Volume
**File:** `bai4/gpt3-pod.yaml`
```yaml
volumes:
  - name: app-config
    configMap:
      name: app-config
```

#### 2.12.5 Secret Volume
**File:** `bai4/gpt6-pod.yaml`
```yaml
volumes:
  - name: secret-mount
    secret:
      secretName: db-secret
```

#### 2.12.6 PersistentVolumeClaim Volume
**File:** `bai20/depl.yaml`
```yaml
volumes:
- name: data-mount
  persistentVolumeClaim:
    claimName: app-pvc
```

---

## 3. PATTERN & BEST PRACTICES

### 3.1 Labels & Selectors
```yaml
# Deployment/RS labels
metadata:
  labels:
    app: web
selector:
  matchLabels:
    app: web
```
**Pattern:** Luôn match labels giữa deployment và pods

### 3.2 Resource Requests & Limits
```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 200m
    memory: 256Mi
```
**Pattern:** Luôn định nghĩa requests & limits cho HPA

### 3.3 Probes Configuration
```yaml
# Liveness (restart pod nếu unhealthy)
livenessProbe:
  exec/httpGet/tcpSocket:
    ...
  initialDelaySeconds: 5
  periodSeconds: 5
  timeoutSeconds: 1
  failureThreshold: 3

# Readiness (remove từ service nếu unready)
readinessProbe:
  ...

# Startup (cấp phép thời gian boot)
startupProbe:
  failureThreshold: 30
  periodSeconds: 10
```

### 3.4 Environment Variables từ ConfigMap/Secret
```yaml
# Method 1: Load toàn bộ ConfigMap/Secret
envFrom:
  - configMapRef:
      name: app-config
  - secretRef:
      name: app-secret

# Method 2: Select specific keys
env:
  - name: APP_ENV
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: APP_ENV
  - name: DB_PASS
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: DB_PASS

# Method 3: Pod field references
  - name: POD_NAME
    valueFrom:
      fieldRef:
        fieldPath: metadata.name
```

### 3.5 Volume Mounting
```yaml
volumeMounts:
  - name: data
    mountPath: /data           # Path inside container
volumes:
  - name: data
    emptyDir/hostPath/configMap/secret/persistentVolumeClaim:
      ...
```

### 3.6 Service Selector Pattern
```yaml
metadata:
  name: web-service
spec:
  selector:
    app: web                   # Match pod labels
  ports:
    - port: 80                 # Service port
      targetPort: 80           # Pod port
      protocol: TCP
```

### 3.7 NetworkPolicy Pattern
```yaml
podSelector:
  matchLabels:
    app: backend
policyTypes:
  - Ingress
  - Egress
ingress:
  - from:
    - podSelector/namespaceSelector
    ports:
      - protocol: TCP
        port: 80
```

---

## 4. DIRECTORY BREAKDOWN

| Thư mục | Nội dung | Đặc điểm |
|---------|---------|---------|
| **bai3** | Pod, Deployment cơ bản | Basic examples |
| **bai4** | ConfigMap, Secret, Pod | CM/Secret usage |
| **bai5** | Env variables, Pod | fieldRef, configMapKeyRef, secretKeyRef |
| **bai6** | Deployment, ReplicaSet | Deployment pattern |
| **bai7-8** | Multi-Pod scenarios | Pod features |
| **bai9** | Shared volumes | emptyDir sharing |
| **bai10** | Multi-container pods | Resources |
| **bai13** | Probes (liveness, readiness, startup) | Health checks |
| **bai14** | HPA, Deployment | Autoscaling |
| **bai15** | Services (ClusterIP, NodePort) | Service types |
| **bai16** | Ingress, NetworkPolicy | Advanced networking |
| **bai17** | Services, Pods | Service application |
| **bai18** | Volumes (emptyDir, hostPath) | Storage |
| **bai19** | PV, PVC | Persistent storage |
| **bai20** | Full stack (Depl + Service + Ingress + PVC) | Complete setup |

---

## 5. TOP EXAMPLES UNTUK SETIAP RESOURCE

| Resource | File | Why Best |
|----------|------|---------|
| **Pod** | `bai13/probe-pod.yaml` | Semua 3 probe types |
| **Deployment** | `bai6/bai1-deployment.yaml` | Resources + replicas + selectors |
| **Service (ClusterIP)** | `bai15/nginx-svc.yaml` | Basic structure |
| **Service (NodePort)** | `bai15/nginx-svc-nodeport.yaml` | Explicit nodePort |
| **ConfigMap** | `bai4/configmap.yaml` | Literal data |
| **Secret** | `bai4/secret.yaml` | Base64 data |
| **HPA** | `bai14/lab-hpa.yaml` | CPU-based scaling |
| **Ingress** | `bai16/ingress.yaml` | Host + path routing |
| **NetworkPolicy** | `bai16/allow-http.yaml` | Allow specific pods |
| **PV** | `bai19/pv.yaml` | hostPath + RWO |
| **PVC** | `bai19/pvc.yaml` | Basic claim |
| **Volume (emptyDir)** | `bai9/share-volume.yaml` | Multi-container sharing |
| **Volume (hostPath)** | `bai18/pod-host-path.yaml` | DirectoryOrCreate |
| **ReplicaSet** | `bai6/lab-replicaset.yaml` | Basic RS |

---

## 6. CHEAT SHEET - RESOURCE SYNTAX

### Pod Cơ Bản
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: myapp
spec:
  containers:
  - name: app
    image: nginx:latest
    ports:
    - containerPort: 80
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 200m
        memory: 256Mi
  restartPolicy: Always
```

### Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: app
        image: nginx
```

### Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-svc
spec:
  type: ClusterIP  # ClusterIP, NodePort, LoadBalancer
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
```

### ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  KEY: value
  config.txt: |
    line1
    line2
```

### Secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  password: c2VjcmV0  # base64 encoded
```

---

## 7. THỐNG KÊ TỆPSYSTEM

**Tổng số files:** 89 YAML files

**Phân bố:**
- bai3: 5 files (Pod, Deployment)
- bai4: 9 files (ConfigMap, Secret, Pod)
- bai5: 9 files (Env vars, ConfigMap, Secret)
- bai6: 3 files (Deployment, ReplicaSet)
- bai9: 3 files (Volumes, networking)
- bai10: 1 file (Multi-pod)
- bai13: 3 files (Probes)
- bai14: 4 files (HPA, Deployment, Service)
- bai15: 14 files (Services)
- bai16: 12 files (Ingress, NetworkPolicy, Deployment, Pod)
- bai17: 4 files (Services, Pods)
- bai18: 2 files (Volumes)
- bai19: 3 files (PV, PVC, Pod)
- bai20: 8 files (Complete stack)

---

## 8. NEXT STEPS - STUDY PATHWAY

### Week 1: Basics
1. Pod cơ bản (bai3, bai4)
2. Deployment, ReplicaSet (bai6)
3. Service types (bai14, bai15)

### Week 2: Configuration
4. ConfigMap & Secret (bai4, bai5)
5. Environment variables (bai5)
6. Volumes (bai9, bai18, bai19)

### Week 3: Advanced
7. Probes (bai13)
8. HPA (bai14)
9. Ingress (bai16, bai20)

### Week 4: Networking & Security
10. NetworkPolicy (bai16)
11. PV/PVC (bai19, bai20)
12. Integration (bai20)


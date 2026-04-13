# CKAD Kubernetes Patterns & Best Practices

Lấy từ phân tích workspace `/home/hanel/k8s/CertCKAD`

---

## 1. POD PATTERNS

### 1.1 Basic Pod
**Reference:** `bai3/example-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
```
✅ **Pattern:** Minimal pod spec

---

### 1.2 Pod With Port
**Reference:** `bai3/bai1.yml`
```yaml
spec:
  containers:
  - name: nginx
    image: nginx:1.25
    ports:
    - containerPort: 80
  restartPolicy: Always
```
✅ **Pattern:** Expose port, set restart policy

---

### 1.3 Pod With Labels
**Reference:** `bai3/nginx-deployment.yaml`
```yaml
metadata:
  labels:
    run: nginx
spec:
  containers:
  - image: nginx:1.25
    name: nginx
```
✅ **Pattern:** Use labels for selection

---

### 1.4 Pod With resource Requests & Limits
**Reference:** `bai10/multi-pod.yaml`
```yaml
spec:
  containers:
  - image: nginx
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 200m
        memory: 256Mi
```
✅ **Pattern:** Always define resources for:
  - Proper Kubernetes scheduling
  - HPA metrics
  - Node resource planning

---

### 1.5 Multi-Container Pod
**Reference:** `bai10/multi-pod.yaml`
```yaml
spec:
  containers:
  - image: nginx
    name: nginx
  - image: busybox
    name: logging
    resources:
      requests: {cpu: 100m, memory: 128Mi}
      limits: {cpu: 200m, memory: 256Mi}
```
✅ **Pattern:** Multi-container sidecar pattern

---

## 2. HEALTH CHECKS (PROBES)

### 2.1 Liveness Probe - Exec
**Reference:** `bai13/liveness-pod.yaml`
```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/health
  initialDelaySeconds: 5
  periodSeconds: 5
```
✅ **When to use:** Check file exists, process running

---

### 2.2 Liveness Probe - HTTP
**Reference:** `bai13/probe-pod.yaml`
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 80
    scheme: HTTP
    httpHeaders:
    - name: Custom-Header
      value: Awesome
  initialDelaySeconds: 3
  periodSeconds: 3
  timeoutSeconds: 1
  failureThreshold: 3
```
✅ **When to use:** REST API health endpoints

---

### 2.3 Readiness Probe - Exec
**Reference:** `bai13/probe-pod.yaml`
```yaml
readinessProbe:
  exec:
    command: ["cat", "/tmp/healthy"]
  initialDelaySeconds: 5
  periodSeconds: 5
```
✅ **When to use:** Check if ready to serve traffic

---

### 2.4 Startup Probe - TCP
**Reference:** `bai13/probe-pod.yaml`
```yaml
startupProbe:
  tcpSocket:
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```
✅ **When to use:** Long startup time (30 * 10 = 300s)

---

### 2.5 Complete Probe Configuration
**Reference:** `bai13/probe-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: probe-pod
spec:
  containers:
  - name: app
    image: nginx
    # Restart pod if not healthy
    livenessProbe:
      httpGet: {path: /healthz, port: 80}
      initialDelaySeconds: 3
      periodSeconds: 3
      timeoutSeconds: 1
      failureThreshold: 3
    # Remove from service if not ready
    readinessProbe:
      exec:
        command: ["cat", "/tmp/healthy"]
      initialDelaySeconds: 5
      periodSeconds: 5
    # Give time to startup
    startupProbe:
      tcpSocket: {port: 8080}
      failureThreshold: 30
      periodSeconds: 10
```

---

## 3. ENVIRONMENT VARIABLES

### 3.1 Literal Value
```yaml
env:
- name: SIMPLE
  value: "hello"
```

### 3.2 Pod Field Reference
**Reference:** `bai5/env-command-pod.yaml`
```yaml
env:
- name: POD_NAME
  valueFrom:
    fieldRef:
      fieldPath: metadata.name
- name: POD_NAMESPACE
  valueFrom:
    fieldRef:
      fieldPath: metadata.namespace
- name: POD_IP
  valueFrom:
    fieldRef:
      fieldPath: status.podIP
```

### 3.3 ConfigMap Reference
**Reference:** `bai5/env-command-pod.yaml`
```yaml
# Single key
env:
- name: APP_MODE
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: APP_MODE
      optional: false

# All keys
envFrom:
- configMapRef:
    name: app-config
```

### 3.4 Secret Reference
**Reference:** `bai5/env-command-pod.yaml`
```yaml
# Single key
env:
- name: DB_PASS
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: DB_PASS

# All keys
envFrom:
- secretRef:
    name: db-secret
```

### 3.5 Combined Everything
**Reference:** `bai4/baitap-pod.yaml`
```yaml
containers:
- image: busybox
  env:
  # Literal
  - name: ENV_LOCAL
    value: "local-value"
  
  # Field reference
  - name: ENV_NAME_POD
    valueFrom:
      fieldRef:
        fieldPath: metadata.name
  
  # ConfigMap single key
  - name: ENV_VAR_CONFIG
    valueFrom:
      configMapKeyRef:
        name: baitap-config
        key: APP_ENV
  
  # Secret single key
  - name: ENV_VAR_SECRET
    valueFrom:
      secretKeyRef:
        name: baitap-secret
        key: DB_USER
  
  # envFrom - load all
  envFrom:
  - configMapRef:
      name: baitap-config
  - secretRef:
      name: baitap-secret
  
  # Volumes
  volumeMounts:
  - name: config-volume
    mountPath: /etc/config
  - name: secret-volume
    mountPath: /etc/secret

volumes:
- name: config-volume
  configMap:
    name: baitap-config
- name: secret-volume
  secret:
    secretName: baitap-secret
```

---

## 4. VOLUME PATTERNS

### 4.1 EmptyDir - Single Container
```yaml
spec:
  volumes:
  - name: temp
    emptyDir: {}
  containers:
  - volumeMounts:
    - name: temp
      mountPath: /tmp
```
✅ **Use:** Temporary files, cache

---

### 4.2 EmptyDir - Shared Between Containers
**Reference:** `bai9/share-volume.yaml`
```yaml
spec:
  containers:
  - name: writer
    image: busybox
    volumeMounts:
    - name: shared
      mountPath: /output
  - name: reader
    image: busybox
    volumeMounts:
    - name: shared
      mountPath: /input
  volumes:
  - name: shared
    emptyDir: {}
```
✅ **Pattern:** Multi-container IPC

---

### 4.3 EmptyDir - Memory Backed
**Reference:** `bai18/pod-empty-dir.yaml`
```yaml
volumes:
- name: temp
  emptyDir:
    medium: Memory
    sizeLimit: 10Mi
```
✅ **Use:** High-performance temporary storage

---

### 4.4 HostPath - Directory
**Reference:** `bai18/pod-host-path.yaml`
```yaml
spec:
  volumes:
  - name: host-vol
    hostPath:
      path: /tmp/data
      type: DirectoryOrCreate
  containers:
  - volumeMounts:
    - name: host-vol
      mountPath: /data
```
✅ **Types:**
  - `Directory` - must exist
  - `DirectoryOrCreate` - create if not exists
  - `File` - must exist
  - `FileOrCreate` - create if not exists

---

### 4.5 ConfigMap as Volume
**Reference:** `bai4/gpt3-pod.yaml`
```yaml
spec:
  volumes:
  - name: config
    configMap:
      name: app-config
  containers:
  - volumeMounts:
    - name: config
      mountPath: /etc/config
      readOnly: true
```
✅ **Result:** ConfigMap keys become files in `/etc/config/`

---

### 4.6 Secret as Volume
**Reference:** `bai4/gpt6-pod.yaml`
```yaml
spec:
  volumes:
  - name: secret
    secret:
      secretName: db-secret
  containers:
  - volumeMounts:
    - name: secret
      mountPath: /etc/secret
      readOnly: true
```
✅ **Result:** Secret keys become files in `/etc/secret/`

---

### 4.7 PersistentVolumeClaim
**Reference:** `bai20/depl.yaml`
```yaml
spec:
  volumes:
  - name: data
    persistentVolumeClaim:
      claimName: app-pvc
  containers:
  - volumeMounts:
    - name: data
      mountPath: /data
```

---

## 5. DEPLOYMENT PATTERNS

### 5.1 Basic Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: nginx
```

### 5.2 Deployment With Resources
**Reference:** `bai6/bai1-deployment.yaml`
```yaml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
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
✅ **Pattern:** Always define resources

---

### 5.3 Deployment With Namespace
**Reference:** `bai16/depl.yaml`
```yaml
metadata:
  name: web
  namespace: client
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
```

---

### 5.4 Deployment With PVC
**Reference:** `bai20/depl.yaml`
```yaml
spec:
  template:
    spec:
      containers:
      - image: nginx:alpine
        volumeMounts:
        - name: data
          mountPath: /data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: app-pvc
```

---

## 6. SERVICE PATTERNS

### 6.1 ClusterIP (Default)
**Reference:** `bai15/nginx-svc.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 80
```
✅ **Internal only**, no external access

---

### 6.2 NodePort
**Reference:** `bai15/nginx-svc-nodeport.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
```
✅ **Access:** `<NODE_IP>:30080`
✅ **Range:** 30000-32767

---

### 6.3 Service With External Endpoints
**Reference:** `bai15/backend-external.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-backend
spec:
  ports:
  - port: 80
    targetPort: 8080
---
apiVersion: v1
kind: Endpoints
metadata:
  name: external-backend
subnets:
- addresses:
  - ip: 203.0.113.10
  ports:
  - port: 8080
```
✅ **Pattern:** Route to external services

---

## 7. CONFIGMAP & SECRET

### 7.1 ConfigMap Literal
**Reference:** `bai4/configmap.yaml`
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: production
```

---

### 7.2 ConfigMap Multi-Key
**Reference:** `bai4/baitap-config.yaml`
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: baitap-config
data:
  APP_ENV: staging
  APP_VERSION: 1.2.3
```

---

### 7.3 Secret Base64
**Reference:** `bai4/secret.yaml`
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
data:
  DB_PASS: MTIzNDU2       # echo -n "123456" | base64
  DB_USER: YWRtaW4=       # echo -n "admin" | base64
```
✅ **Encode:** `echo -n "value" | base64`
✅ **Decode:** `echo "base64value" | base64 -d`

---

## 8. HPA PATTERN

**Reference:** `bai14/lab-hpa.yaml`
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: front-end
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```
✅ **Requirements:**
  - Deployment must have `resources.requests.cpu`
  - Min ≤ replicas ≤ Max

---

## 9. INGRESS PATTERN

### 9.1 Host-Based Routing
**Reference:** `bai16/ingress.yaml`
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
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-svc
            port:
              number: 80
```

---

### 9.2 Path-Based Routing
**Reference:** `bai20/ingress.yaml`
```yaml
spec:
  rules:
  - http:
      paths:
      - path: /final
        pathType: Prefix
        backend:
          service:
            name: app-final
            port:
              number: 80
```

---

## 10. NETWORK POLICY PATTERNS

### 10.1 Deny All Ingress
**Reference:** `bai16/deny-all.yaml`
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
✅ **Effect:** Default deny all incoming traffic

---

### 10.2 Allow Specific Pod
**Reference:** `bai16/allow-http.yaml`
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-http
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
✅ **Effect:** frontend pods can access backend:80

---

### 10.3 Allow Specific Namespace
**Reference:** `bai16/allow-namespace.yaml`
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-namespace
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
✅ **Effect:** All pods in `client` namespace can access backend
✅ **Setup:** `kubectl label ns client name=client`

---

### 10.4 Deny All Egress
**Reference:** `bai16/deny-egress.yaml`
```yaml
spec:
  podSelector: {}
  policyTypes:
  - Egress
```
✅ **Effect:** No outgoing traffic allowed

---

## 11. PV/PVC PATTERN

### 11.1 PersistentVolume
**Reference:** `bai19/pv.yaml`
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

---

### 11.2 PersistentVolumeClaim
**Reference:** `bai19/pvc.yaml`
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

---

### 11.3 Pod Using PVC
```yaml
spec:
  volumes:
  - name: data
    persistentVolumeClaim:
      claimName: local-pvc
  containers:
  - volumeMounts:
    - name: data
      mountPath: /data
```

---

## QUICK REFERENCE TABLE

| Pattern | Use Case | File |
|---------|----------|------|
| Basic Pod | Simple container | bai3/example-pod.yaml |
| Pod + Probes | Health checks | bai13/probe-pod.yaml |
| Pod + Env | Configuration | bai5/env-command-pod.yaml |
| Multi-container | Sidecar pattern | bai10/multi-pod.yaml |
| Deployment | Managed replicas | bai6/bai1-deployment.yaml |
| Service ClusterIP | Internal routing | bai15/nginx-svc.yaml |
| Service NodePort | External access | bai15/nginx-svc-nodeport.yaml |
| ConfigMap | Configuration | bai4/configmap.yaml |
| Secret | Sensitive data | bai4/secret.yaml |
| HPA | Auto-scaling | bai14/lab-hpa.yaml |
| Ingress | HTTP routing | bai16/ingress.yaml |
| NetworkPolicy Allow | Network security | bai16/allow-http.yaml |
| NetworkPolicy Deny | Block traffic | bai16/deny-all.yaml |
| EmptyDir Volume | Temp storage | bai9/share-volume.yaml |
| HostPath Volume | Node storage | bai18/pod-host-path.yaml |
| PV/PVC | Persistent storage | bai19/pv.yaml + bai19/pvc.yaml |


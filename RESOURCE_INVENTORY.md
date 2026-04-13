# Kubernetes Resource Types - Quick Reference

## RESOURCE INVENTORY MAPPING

### POD (14 examples)
- ✅ `bai3/example-pod.yaml` - Basic pod
- ✅ `bai13/pod.yaml` - Pod with liveness probe
- ✅ `bai13/liveness-pod.yaml` - Liveness exec probe
- ✅ `bai13/probe-pod.yaml` - ⭐ All 3 probes (httpGet, exec, tcpSocket)
- ✅ `bai4/gpt2-pod.yaml` - Pod with envFrom
- ✅ `bai4/gpt3-pod.yaml` - Pod with ConfigMap volume
- ✅ `bai4/gpt6-pod.yaml` - Pod with Secret volume
- ✅ `bai4/pod.yaml` - Basic pod
- ✅ `bai4/baitap-pod.yaml` - ⭐ Advanced pod (env, volumes, ConfigMap, Secret)
- ✅ `bai5/env-command-pod.yaml` - ⭐ Environment variables (fieldRef, configMapKeyRef, secretKeyRef, envFrom)
- ✅ `bai5/bai4-pod.yaml` - Pod with configMapRef
- ✅ `bai5/bai5-pod.yaml` - Pod with secretRef
- ✅ `bai10/multi-pod.yaml` - ⭐ Multi-container with resources
- ✅ `bai9/share-volume.yaml` - Multi-container shared emptyDir
- ✅ `bai19/pod.yaml` - Pod with PVC volume
- ✅ `bai16/backend.yaml` - Basic pod in namespace
- ✅ `bai16/frontend.yaml` - Basic pod in namespace

### DEPLOYMENT (10 examples)
- ✅ `bai3/nginx-deployment.yaml` - Basic deployment (auto-created from pod)
- ✅ `bai6/bai1-deployment.yaml` - ⭐ Standard deployment with resources & replicas
- ✅ `bai6/lab-deployment.yaml` - Deployment basic
- ✅ `bai14/lab-deploy.yaml` - ⭐ Deployment for HPA (with resource requests/limits)
- ✅ `bai14/nginx-deploy.yaml` - Deployment basic
- ✅ `bai15/exam-depl.yaml` - Deployment basic
- ✅ `bai15/gpt1-depl.yaml` - Deployment basic
- ✅ `bai15/gpt2-depl.yaml` - Deployment basic
- ✅ `bai15/lab1-depl.yaml` - Deployment basic
- ✅ `bai15/nginx-depl.yaml` - Deployment basic
- ✅ `bai16/depl.yaml` - ⭐ Deployment with namespace
- ✅ `bai20/depl.yaml` - ⭐ Deployment with PVC volume

### REPLICASET (1 example)
- ✅ `bai6/lab-replicaset.yaml` - ⭐ ReplicaSet basic

### SERVICE - ClusterIP (8 examples)
- ✅ `bai14/lab-service.yaml` - ⭐ Service basic (ClusterIP)
- ✅ `bai14/nginx-service.yaml` - Service basic
- ✅ `bai15/nginx-svc.yaml` - ⭐ Service ClusterIP (port:8080 → targetPort:80)
- ✅ `bai15/exam-svc.yaml` - Service basic
- ✅ `bai15/gpt1-svc.yaml` - Service basic
- ✅ `bai15/gpt2-svc.yaml` - Service basic
- ✅ `bai15/lab1-svc.yaml` - Service basic
- ✅ `bai16/svc.yaml` - Service basic
- ✅ `bai17/svc.yaml` - ⭐ Service basic

### SERVICE - NodePort (5 examples)
- ✅ `bai15/nginx-svc-nodeport.yaml` - ⭐ Service NodePort (port:80, targetPort:80, nodePort:30080)
- ✅ `bai15/exam-svc-ext.yaml` - ⭐ Service NodePort (port:8080 → targetPort:80, nodePort:30081)
- ✅ `bai17/svc-nodeport.yaml` - Service NodePort

### SERVICE - External (1 example)
- ✅ `bai15/backend-external.yaml` - Service + Endpoints (external IP)

### CONFIGMAP (6 examples)
- ✅ `bai4/configmap.yaml` - ⭐ ConfigMap literal data (key: value)
- ✅ `bai4/gpt1-cm.yaml` - ConfigMap basic
- ✅ `bai4/baitap-config.yaml` - ⭐ ConfigMap with multiple keys
- ✅ `bai5/app-configmap.yaml` - ConfigMap basic
- ✅ `bai5/bai4-configmap.yaml` - ConfigMap basic

### SECRET (6 examples)
- ✅ `bai4/secret.yaml` - ⭐ Secret base64 data (DB credentials)
- ✅ `bai4/baitap-secret.yaml` - Secret basic
- ✅ `bai4/gpt4-sc.yaml` - Secret/StorageClass
- ✅ `bai5/app-secret.yaml` - Secret basic
- ✅ `bai5/bai5-secret.yaml` - Secret basic

### HORIZONTAL POD AUTOSCALER (1 example)
- ✅ `bai14/lab-hpa.yaml` - ⭐ HPA (autoscaling/v2, CPU-based, minReplicas:1, maxReplicas:5)
- ✅ `bai14/nginx-hpa.yml` - HPA basic

### INGRESS (3 examples)
- ✅ `bai16/ingress.yaml` - ⭐ Ingress host + path rules
- ✅ `bai20/ingress.yaml` - ⭐ Ingress with namespace & path routing

### NETWORKPOLICY (7 examples)
- ✅ `bai16/deny-all.yaml` - ⭐ NetworkPolicy deny all ingress
- ✅ `bai16/deny-egress.yaml` - ⭐ NetworkPolicy deny all egress
- ✅ `bai16/allow-http.yaml` - ⭐ NetworkPolicy allow specific pod + port
- ✅ `bai16/allow-namespace.yaml` - ⭐ NetworkPolicy allow specific namespace
- ✅ `bai16/allow-frontend.yaml` - NetworkPolicy allow frontend
- ✅ `bai16/allow-label.yaml` - (File not found - may be deleted)

### PERSISTENT VOLUME (1 example)
- ✅ `bai19/pv.yaml` - ⭐ PersistentVolume (hostPath + manual StorageClass)

### PERSISTENT VOLUME CLAIM (2 examples)
- ✅ `bai19/pvc.yaml` - ⭐ PersistentVolumeClaim basic
- ✅ `bai20/pvc.yaml` - ⭐ PersistentVolumeClaim (with namespace)

### VOLUMES - EmptyDir (3 examples)
- ✅ `bai9/share-volume.yaml` - ⭐ emptyDir shared between containers
- ✅ `bai18/pod-empty-dir.yaml` - ⭐ emptyDir with Memory medium & sizeLimit

### VOLUMES - HostPath (1 example)
- ✅ `bai18/pod-host-path.yaml` - ⭐ hostPath (path:/tmp/data, type:DirectoryOrCreate)

### VOLUMES - ConfigMap (1 example)
- ✅ `bai4/gpt3-pod.yaml` - ⭐ Volume type: configMap

### VOLUMES - Secret (1 example)
- ✅ `bai4/gpt6-pod.yaml` - ⭐ Volume type: secret

### VOLUMES - PersistentVolumeClaim (1 example)
- ✅ `bai20/depl.yaml` - ⭐ Volume type: persistentVolumeClaim

### NAMESPACE & CONFIG (3 examples)
- ✅ `bai16/ns-client.yaml` - Namespace client
- ✅ Root files: 
  - `example-configmap-secret-pod.yaml` - Example pod with ConfigMap + Secret

---

## STATISTICS

- **Total YAML files:** 89+
- **Resource Types:** 12
  - Pod: 17
  - Deployment: 12
  - Service (ClusterIP): 9
  - Service (NodePort): 5
  - ConfigMap: 6
  - Secret: 5
  - Ingress: 2
  - NetworkPolicy: 7
  - HPA: 2
  - PV: 1
  - PVC: 2
  - ReplicaSet: 1
- **NOT FOUND:** StatefulSet, DaemonSet, Job, CronJob, LoadBalancer, ExternalName services

---

## API VERSIONS USED

```
v1                      - Pod, Service, ConfigMap, Secret, PV, PVC
apps/v1                 - Deployment, ReplicaSet
autoscaling/v2          - HorizontalPodAutoscaler
networking.k8s.io/v1    - NetworkPolicy, Ingress
```

---

## BEST PRACTICES IDENTIFIED

### Container Resources
```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 200m
    memory: 256Mi
```
✅ Every Deployment/Pod should define requests & limits

### Health Checks
```yaml
livenessProbe:      # Restart if not healthy
readinessProbe:     # Remove from service if not ready
startupProbe:       # Give time to startup
```
✅ Use appropriate probe types for different scenarios

### ConfigMap & Secret
```yaml
envFrom:
  - configMapRef: name
  - secretRef: name
env:
  - valueFrom:
      configMapKeyRef/secretKeyRef
      fieldRef  # For Pod metadata
```
✅ Multiple ways to inject configuration

### Volumes
```yaml
emptyDir           # Ephemeral, shared between containers
hostPath           # Persistent, but node-specific
configMap/secret   # Configuration as files
persistentVolume   # Persistent across pod restarts
```
✅ Choose volume type based on use case

### Service Selector
```yaml
selector:
  app: myapp      # Must match pod labels
```
✅ Service routes traffic using selector labels

### NetworkPolicy
```yaml
podSelector: matchLabels     # Target pods
ingress/egress: from: ...    # Traffic direction & source
ports: ...                   # Specific ports
```
✅ Use podSelector & namespaceSelector appropriately

---

## QUICK LOOKUP BY FILE FOLDER

### `/bai3/` - Kubernetes Basics
- Pod creation, basic deployment patterns

### `/bai4/` - Configuration Management
- ConfigMap & Secret creation and usage

### `/bai5/` - Environment Configuration
- Advanced env variable injection, fieldRef, configMapKeyRef, secretKeyRef

### `/bai6/` - Scaling & Replica Control
- Deployment, ReplicaSet patterns

### `/bai9/` - Networking & Volumes
- Pod-to-pod communication, shared volumes

### `/bai10/` - Multi-Container Pods
- Multi-container patterns, sidecar containers

### `/bai13/` - Health Checks
- Liveness, readiness, startup probes

### `/bai14/` - Horizontal Pod Autoscaling
- HPA configuration, resource metrics

### `/bai15/` - Service Types
- ClusterIP, NodePort, service ports mapping

### `/bai16/` - Ingress & Network Policies
- Ingress routing, network security policies

### `/bai17/` - Service Application
- Practical service examples

### `/bai18/` - Volume Types
- EmptyDir, HostPath volumes

### `/bai19/` - Persistent Storage
- PersistentVolume, PersistentVolumeClaim

### `/bai20/` - Complete Stack
- Full example: Deployment + Service + Ingress + PVC


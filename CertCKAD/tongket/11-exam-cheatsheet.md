# ============================================================================
# CKAD EXAM CHEATSHEET - ESSENTIAL COMMANDS AND TIPS
# ============================================================================

## KUBECTL BASIC COMMANDS

### Getting Resources
```bash
# List resources
kubectl get pods                           # Get pods in current namespace
kubectl get pod -o wide                    # Detailed output
kubectl get pods --all-namespaces          # All namespaces
kubectl get pods -n <namespace>            # Specific namespace
kubectl get pods -l app=frontend           # Filter by label
kubectl get pods --sort-by=.metadata.name  # Sort by name
kubectl get pods --field-selector status.phase=Running  # Filter by field

# List all resource types
kubectl get all
kubectl get all -n <namespace>

# Describe resource (detailed info)
kubectl describe pod <pod-name>
kubectl describe pod <pod-name> -n <namespace>

# Get YAML definition
kubectl get pod <pod-name> -o yaml
kubectl get pod <pod-name> -o json

# Get specific fields
kubectl get pod <pod-name> -o jsonpath='{.metadata.name}'
kubectl get pod <pod-name> -o jsonpath='{.spec.containers[0].name}'
kubectl get pod <pod-name> -o jsonpath='{.status.podIP}'
```

### Creating Resources from CLI
```bash
# Pod
kubectl run <pod-name> --image=<image> --dry-run=client -o yaml > pod.yaml
kubectl run <pod-name> --image=<image> -n <namespace> --dry-run=client -o yaml

# Deployment
kubectl create deployment <name> --image=<image> --replicas=3 --dry-run=client -o yaml > deploy.yaml

# Service
kubectl expose deployment <deployment-name> --port=80 --target-port=8080 --type=ClusterIP --dry-run=client -o yaml

# ConfigMap
kubectl create configmap <name> --from-literal=key=value --dry-run=client -o yaml
kubectl create configmap <name> --from-file=config.yaml --dry-run=client -o yaml

# Secret
kubectl create secret generic <name> --from-literal=key=value --dry-run=client -o yaml
kubectl create secret tls <name> --cert=file.crt --key=file.key --dry-run=client -o yaml

# Namespace
kubectl create namespace <name> --dry-run=client -o yaml

# RBAC
kubectl create role <name> --verb=get,list,watch --resource=pods --dry-run=client -o yaml
kubectl create rolebinding <name> --role=<role> --serviceaccount=<sa> --dry-run=client -o yaml

# HPA
kubectl autoscale deployment <name> --min=2 --max=10 --cpu-percent=50 --dry-run=client -o yaml

# Ingress
kubectl create ingress <name> --rule='host/path=service:port' --dry-run=client -o yaml
```

### Modifying Resources
```bash
# Edit resource in editor
kubectl edit pod <pod-name>
kubectl edit deployment <deployment-name>

# Apply YAML file
kubectl apply -f file.yaml
kubectl apply -f directory/

# Delete resource
kubectl delete pod <pod-name>
kubectl delete deployment <name>
kubectl delete -f file.yaml

# Replace resource (delete and recreate)
kubectl replace -f file.yaml --force

# Patch resource (modify specific field)
kubectl patch pod <name> -p '{"spec":{"containers":[{"name":"web","image":"nginx:latest"}]}}'

# Label resource
kubectl label pod <name> app=frontend
kubectl label pod <name> app=frontend --overwrite

# Unlabel resource
kubectl label pod <name> app-

# Annotate resource
kubectl annotate pod <name> key=value
```

---

## CONTAINER DEBUGGING COMMANDS

```bash
# Port forward to pod
kubectl port-forward pod/<pod-name> 8080:80
kubectl port-forward svc/<service-name> 8080:80
kubectl port-forward deployment/<deployment-name> 8080:80

# Exec into pod
kubectl exec -it <pod-name> -- /bin/sh
kubectl exec -it <pod-name> -c <container-name> -- /bin/sh
kubectl exec -it <pod-name> -- ls -la

# Logs
kubectl logs <pod-name>
kubectl logs <pod-name> -c <container-name>        # Specific container
kubectl logs <pod-name> -f                         # Follow logs
kubectl logs <pod-name> --tail=100                 # Last 100 lines
kubectl logs <pod-name> --since=10m                # Since 10 minutes ago
kubectl logs <pod-name> --timestamps               # With timestamps
kubectl logs -l app=frontend                       # Logs from pods with label

# Copy files to/from pod
kubectl cp <pod-name>:/path/to/file ./local/path
kubectl cp ./local/path <pod-name>:/path/to/file

# Run temporary pod
kubectl run -it --rm debug --image=alpine --restart=Never -- /bin/sh
kubectl run -it --rm debug --image=busybox --restart=Never -- wget -q -O- http://service:8080
```

---

## DEPLOYMENTS AND SCALING

```bash
# Scale deployment
kubectl scale deployment <name> --replicas=5
kubectl patch deployment <name> -p '{"spec":{"replicas":3}}'

# Update image
kubectl set image deployment/<name> <container-name>=<new-image>
kubectl set image deployment/<name> <container-name>=nginx:2.0 --record

# Rollout status
kubectl rollout status deployment/<name>

# Rollout history
kubectl rollout history deployment/<name>
kubectl rollout history deployment/<name> --revision=2

# Rollback
kubectl rollout undo deployment/<name>                 # To previous version
kubectl rollout undo deployment/<name> --to-revision=2

# Restart pods (recreate)
kubectl rollout restart deployment/<name>
```

---

## RESOURCE MANAGEMENT

```bash
# Get resource usage
kubectl top pods
kubectl top pods -n <namespace>
kubectl top nodes
kubectl top node <node-name>

# Resource quota
kubectl get resourcequota -n <namespace>
kubectl describe resourcequota <name> -n <namespace>

# Limit range
kubectl get limitrange -n <namespace>

# Check pod resource requests/limits
kubectl get pod <name> -o jsonpath='{.spec.containers[].resources}'
```

---

## CONTEXT AND CLUSTER

```bash
# View current context
kubectl config current-context

# List all contexts
kubectl config get-contexts

# Switch context
kubectl config use-context <context-name>

# Get cluster info
kubectl cluster-info
kubectl get nodes
kubectl describe node <node-name>

# Get API resources
kubectl api-resources
kubectl api-versions
```

---

## ADVANCED DEBUGGING

```bash
# Get events
kubectl get events
kubectl get events -n <namespace>
kubectl get events --all-namespaces
kubectl get events -w                    # Watch events
kubectl get events --sort-by='.lastTimestamp'

# Check pod conditions
kubectl get pod <name> -o jsonpath='{.status.conditions}'

# Check container statuses
kubectl get pod <name> -o jsonpath='{.status.containerStatuses}'

# Get pod detailed status
kubectl describe pod <name>              # Shows Events section

# Test DNS from pod
kubectl run -it --rm debug --image=alpine --restart=Never -- nslookup kubernetes.default

# Test service connectivity
kubectl run -it --rm debug --image=busybox --restart=Never -- wget -q -O- http://<service-name>:<port>

# Check if metrics-server is running
kubectl get deployment metrics-server -n kube-system
```

---

## IMPORTANT TIPS FOR CKAD EXAM

### 1. RESOURCE REQUESTS/LIMITS (CRITICAL!)
```bash
# HPA requires CPU requests!
resources:
  requests:
    cpu: "250m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "256Mi"
```

### 2. PROBES (ALWAYS INCLUDE IN DEPLOYMENTS)
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /ready
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 5
```

### 3. LABELS AND SELECTORS
```bash
# Always use consistent labels
kubectl label pod <name> app=myapp tier=frontend
kubectl get pods -l app=myapp,tier=frontend
```

### 4. NAMESPACE MANAGEMENT
```bash
# Set default namespace in context
kubectl config set-context --current --namespace=<namespace>

# Create namespace
kubectl create namespace production

# Delete namespace (deletes all resources in it!)
kubectl delete namespace production
```

### 5. ENVIRONMENT VARIABLES - 4 SOURCES
```yaml
env:
# 1. Literal
- name: MY_VAR
  value: "literal-value"

# 2. ConfigMap
- name: CONFIG_VAR
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: key-name

# 3. Secret
- name: SECRET_VAR
  valueFrom:
    secretKeyRef:
      name: app-secret
      key: key-name

# 4. Pod metadata (downwardAPI)
- name: POD_NAME
  valueFrom:
    fieldRef:
      fieldPath: metadata.name
- name: POD_IP
  valueFrom:
    fieldRef:
      fieldPath: status.podIP
```

### 6. VOLUME MOUNTS - ALL TYPES
```yaml
volumes:
# emptyDir - temporary
- name: temp
  emptyDir:
    sizeLimit: 1Gi

# hostPath - node filesystem
- name: host-data
  hostPath:
    path: /var/log
    type: Directory

# ConfigMap - read config files
- name: config
  configMap:
    name: app-config

# Secret - read secrets
- name: secrets
  secret:
    secretName: app-secret

# PVC - persistent storage
- name: data
  persistentVolumeClaim:
    claimName: app-pvc

# downwardAPI - pod metadata as files
- name: podinfo
  downwardAPI:
    items:
    - path: labels
      fieldRef:
        fieldPath: metadata.labels
```

### 7. QUICK RESOURCE CREATION PATTERN
```bash
# Generate YAML without applying
kubectl <command> --dry-run=client -o yaml > file.yaml

# Apply the YAML
kubectl apply -f file.yaml

# Check if it worked
kubectl get <resource>
kubectl describe <resource> <name>
```

### 8. DEBUGGING CHECKLIST
- [ ] Pod status: `kubectl get pod <name>`
- [ ] Pod events: `kubectl describe pod <name>` (scroll to Events)
- [ ] Pod logs: `kubectl logs <name>` 
- [ ] Exec into pod: `kubectl exec -it <name> -- /bin/sh`
- [ ] Verify DNS: `kubectl run -it --rm debug --image=alpine -- nslookup <service>`
- [ ] Check probe endpoints: Port forward and test manually
- [ ] Verify mounts: `kubectl exec -it <name> -- ls -la /mount/path`
- [ ] Check RBAC: `kubectl describe sa <name>`

### 9. TIME MANAGEMENT IN EXAM
- Read the question carefully (2-3 seconds)
- Generate YAML using `--dry-run=client -o yaml` (30 seconds)
- Edit YAML if needed in VIM (1-2 minutes)
- Apply with `kubectl apply -f` (10 seconds)
- Verify with `kubectl get` or `kubectl describe` (10 seconds)
- Move to next question
- **Total per question: ~3-5 minutes**

### 10. VIM TIPS (If editing inline)
```vim
# Enter insert mode
i       # Insert before cursor
a       # Insert after cursor
o       # New line below
O       # New line above

# Exit insert mode
ESC

# Save and exit
:wq     # Write and quit
:q!     # Quit without saving

# Find and replace
:%s/old/new/g

# Copy/paste
yy      # Copy line
p       # Paste
dd      # Delete line
```

### 11. COMMON MISTAKES TO AVOID
- [ ] Missing resource requests for HPA
- [ ] Forgot to mount ConfigMap/Secret as volume
- [ ] Wrong selector in service
- [ ] Using wrong apiVersion
- [ ] Not setting restartPolicy in Job
- [ ] Forgetting headless service for StatefulSet
- [ ] Not including readiness probe
- [ ] Wrong container port vs service port
- [ ] Missing file permissions for secrets (should be 0600)
- [ ] Trying to modify immutable fields (need replace)

### 12. KUBECTL AUTOCOMPLETE (Save Time!)
```bash
# In bash
source <(kubectl completion bash)
alias k=kubectl
complete -F __start_kubectl k

# Then use:
k get po          # Instead of kubectl get pods
k exec -it        # Auto-complete pod names with TAB
```

---

## QUICK REFERENCE TABLE

| Task | Command |
|------|---------|
| List pods | `kubectl get pods` |
| Get details | `kubectl describe pod <name>` |
| Create from template | `kubectl run <name> --image=img --dry-run=client -o yaml` |
| Apply YAML | `kubectl apply -f file.yaml` |
| Edit resource | `kubectl edit pod <name>` |
| Delete resource | `kubectl delete pod <name>` |
| Exec into pod | `kubectl exec -it <name> -- /bin/sh` |
| View logs | `kubectl logs <name>` |
| Scale deployment | `kubectl scale deployment <name> --replicas=5` |
| Port forward | `kubectl port-forward pod/<name> 8080:80` |
| Check probes | `kubectl describe pod <name>` (Events section) |
| Get resource usage | `kubectl top pods` |
| Watch resource | `kubectl get pods -w` |

---

## CKAD EXAM PASSING TIPS

1. **Practice kubectl imperative commands** - You don't have time to write YAML from scratch
2. **Use `--dry-run=client -o yaml`** - Generate templates and edit them
3. **Set VIM settings** - `:set number` and `:set expandtab` 
4. **Know your aliases** - `k`, `kgp`, `kdp`, etc.
5. **Understand resources deeply** - Not just syntax, understand how things work
6. **Practice debugging** - `logs`, `describe`, `exec`, `port-forward`
7. **Verify your work** - Always check if resources are running properly
8. **Time management** - Aim for 3-4 minutes per question
9. **Don't panic on hard questions** - Come back to them later if stuck
10. **Read requirements carefully** - Exam questions are very specific

---

## RECOMMENDED STUDY FOCUS FOR CKAD

| Priority | Topic | Commands |
|----------|-------|----------|
| CRITICAL | Pods & Deployments | create, apply, describe, get, delete |
| CRITICAL | Services | expose, get svc, describe svc |
| CRITICAL | ConfigMap & Secret | create, get, describe |
| CRITICAL | Probes | liveness, readiness probe setup |
| CRITICAL | Resource Requests | CPU, memory, limits |
| HIGH | Volumes (all types) | emptyDir, hostPath, PVC, configMap, secret |
| HIGH | HPA | autoscale, get hpa, describe hpa |
| HIGH | Ingress | create ingress, describe ingress |
| MEDIUM | StatefulSet | get sts, describe sts, volumeClaimTemplates |
| MEDIUM | NetworkPolicy | allow, deny policies |
| MEDIUM | Jobs & CronJobs | create job, get jobs, describe job |
| MEDIUM | RBAC | roles, rolebindings, create sa |

---

## LAST-MINUTE REMINDERS
- [ ] Pods need containers with image
- [ ] Deployments need selector matching template labels
- [ ] Services need selector matching pod labels
- [ ] HPA needs resource requests AND correct selector
- [ ] StatefulSet needs serviceName
- [ ] Job needs completions and parallelism
- [ ] Ingress needs rules and backend services
- [ ] NetworkPolicy needs podSelector
- [ ] Probes need proper endpoints and ports
- [ ] Volumes need matching volumeMounts

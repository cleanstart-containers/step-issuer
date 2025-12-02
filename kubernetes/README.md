# 🔐 Step Issuer - Kubernetes Deployment Guide

Complete Kubernetes deployment guide for the CleanStart Step Issuer container. Step Issuer is a certificate issuer for cert-manager that uses Step Certificates CA.

## 📁 Files

- `deployment.yaml` - Complete deployment manifest (Namespace, ServiceAccount, RBAC, Deployment, Service)
- `README.md` - This documentation

## 🖼️ Image Details

**Image:** `cleanstart/step-issuer:latest-dev`

**Key Features:**
- **Binary Location:** `manager`
- **Command:** The deployment uses `command: ["manager"]` to run the controller
- **User:** `clnstrt` (non-root, UID 1000)
- **Architecture:** `amd64`
- **OS:** `linux`
- **SSL Certificates:** Pre-configured at `/etc/ssl/certs/ca-certificates.crt`

## 🚀 Complete Deployment Steps

### Prerequisites

1. **Kubernetes cluster** (Kind, minikube, k3s, GKE, EKS, AKS, or any other)
2. **kubectl** installed and configured to access your cluster
3. **cert-manager** installed (required for Step Issuer to work)

### Step 1: Verify Kubernetes Cluster

```bash
# Check cluster connectivity
kubectl cluster-info

# Verify nodes are ready
kubectl get nodes

# Check if cert-manager is already installed
kubectl get pods -n cert-manager
```

### Step 2: Install cert-manager (if not already installed)

```bash
# Install cert-manager v1.13.0
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.0/cert-manager.yaml

# Wait for cert-manager to be ready (this may take 1-2 minutes)
kubectl wait --for=condition=ready pod -l app.kubernetes.io/instance=cert-manager -n cert-manager --timeout=300s

# Verify cert-manager is running
kubectl get pods -n cert-manager
```

**Expected Output:**
```
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-xxxxxxxxxx-xxxxx             1/1     Running   0          2m
cert-manager-cainjector-xxxxxxxxxx-xxxxx  1/1     Running   0          2m
cert-manager-webhook-xxxxxxxxxx-xxxxx     1/1     Running   0          2m
```

### Step 3: Install Step Issuer CRDs (Required)

Step Issuer requires Custom Resource Definitions (CRDs) to be installed before the controller can run.

**Clone and Install from Repository (Recommended)**

```bash
# Clone the step-issuer repository
git clone https://github.com/smallstep/step-issuer.git
cd step-issuer

# Install CRDs
kubectl apply -f config/crd/bases/

# Verify CRDs are installed
kubectl get crd stepissuers.certmanager.step.sm
kubectl get crd stepclusterissuers.certmanager.step.sm
```

**Verify CRDs:**
```bash
kubectl get crd | grep certmanager.step.sm
```

**Expected Output:**
```
stepclusterissuers.certmanager.step.sm   2025-11-10T12:00:00Z
stepissuers.certmanager.step.sm          2025-11-10T12:00:00Z
```

### Step 4: Deploy Step Issuer

```bash
# Navigate to the deployment directory
cd containers/step-issuer/kubernetes

# Apply the deployment
kubectl apply -f deployment.yaml
```

**Expected Output:**
```
namespace/step-issuer created
serviceaccount/step-issuer created
clusterrole.rbac.authorization.k8s.io/step-issuer created
clusterrolebinding.rbac.authorization.k8s.io/step-issuer created
deployment.apps/step-issuer created
service/step-issuer created
```

### Step 5: Verify Deployment

```bash
# Watch pod status (press Ctrl+C to exit)
kubectl get pods -n step-issuer -w
```

**Expected Output:**
```
NAME                           READY   STATUS    RESTARTS   AGE
step-issuer-xxxxxxxxxx-xxxxx   1/1     Running   0          30s
```

### Step 6: Check Pod Logs

```bash
# View logs
kubectl logs -n step-issuer -l app=step-issuer

# Follow logs in real-time
kubectl logs -n step-issuer -l app=step-issuer -f
```

**Expected Log Output (Success):**
```
{"level":"info","ts":"2025-11-10T12:25:12Z","logger":"setup","msg":"starting manager"}
{"level":"info","ts":"2025-11-10T12:25:12Z","logger":"controller-runtime.metrics","msg":"Starting metrics server"}
I1110 12:25:13.013341       1 leaderelection.go:257] attempting to acquire leader lease step-issuer/step-issuer-operator-lock...
{"level":"info","ts":"2025-11-10T12:25:13Z","logger":"controller-runtime.metrics","msg":"Serving metrics server","bindAddress":"0.0.0.0:8080","secure":false}
I1110 12:26:46.863678       1 leaderelection.go:271] successfully acquired lease step-issuer/step-issuer-operator-lock
{"level":"info","ts":"2025-11-10T12:26:46Z","msg":"Starting EventSource","controller":"stepclusterissuer","controllerGroup":"certmanager.step.sm","controllerKind":"StepClusterIssuer"}
{"level":"info","ts":"2025-11-10T12:26:46Z","msg":"Starting EventSource","controller":"stepissuer","controllerGroup":"certmanager.step.sm","controllerKind":"StepIssuer"}
{"level":"info","ts":"2025-11-10T12:26:46Z","msg":"Starting EventSource","controller":"certificaterequest","controllerGroup":"cert-manager.io","controllerKind":"CertificateRequest"}
{"level":"info","ts":"2025-11-10T12:26:46Z","msg":"Starting Controller","controller":"stepissuer"}
{"level":"info","ts":"2025-11-10T12:26:46Z","msg":"Starting Controller","controller":"certificaterequest"}
{"level":"info","ts":"2025-11-10T12:26:46Z","msg":"Starting workers","controller":"certificaterequest","worker count":1}
{"level":"info","ts":"2025-11-10T12:26:46Z","msg":"Starting workers","controller":"stepissuer","worker count":1}
{"level":"info","ts":"2025-11-10T12:26:46Z","msg":"Starting Controller","controller":"stepclusterissuer"}
{"level":"info","ts":"2025-11-10T12:26:46Z","msg":"Starting workers","controller":"stepclusterissuer","worker count":1}
```

## 🧪 Testing

### Test 1: Verify Pod is Running

```bash
# Get pod name
POD_NAME=$(kubectl get pods -n step-issuer -l app=step-issuer -o jsonpath='{.items[0].metadata.name}')

# Check pod status
kubectl get pod $POD_NAME -n step-issuer

# View pod logs
kubectl logs $POD_NAME -n step-issuer

# Describe pod for detailed information
kubectl describe pod $POD_NAME -n step-issuer
```

### Test 2: Get Help/Version Information

```bash
# Get pod name
POD_NAME=$(kubectl get pods -n step-issuer -l app=step-issuer -o jsonpath='{.items[0].metadata.name}')

# Run help command
kubectl exec -n step-issuer $POD_NAME -- manager --help

# Check version
kubectl exec -n step-issuer $POD_NAME -- manager --version
```

**Alternative: Test with Docker (local)**
```bash
# Run help command using Docker
docker run --rm --entrypoint="" cleanstart/step-issuer:latest-dev manager --help

# Check version
docker run --rm --entrypoint="" cleanstart/step-issuer:latest-dev manager --version
```

### Test 3: Check Health Endpoints

```bash
# Port-forward to access metrics endpoint
kubectl port-forward -n step-issuer svc/step-issuer 8080:8080

# In another terminal, check metrics
curl http://localhost:8080/metrics

# Note: Health probes use TCP socket checks on port 8080
```

### Test 4: Verify RBAC Permissions

```bash
# Check if service account has proper permissions
kubectl auth can-i get stepissuers --as=system:serviceaccount:step-issuer:step-issuer -n step-issuer
kubectl auth can-i get certificaterequests --as=system:serviceaccount:step-issuer:step-issuer --all-namespaces
kubectl auth can-i get leases --as=system:serviceaccount:step-issuer:step-issuer -n step-issuer
```

### Test 5: Verify Service

```bash
# Check service endpoints
kubectl get endpoints -n step-issuer step-issuer

# Check service details
kubectl get service -n step-issuer step-issuer -o yaml
```


## 🔧 Configuration Details

### Command Configuration

- **Binary Path:** `manager`
- **Deployment Command:** `command: ["manager"]`
- **Arguments:** `--enable-leader-election --metrics-addr=0.0.0.0:8080`

### Environment Variables

- `SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt` - SSL certificate path

### Command Arguments

- `--enable-leader-election` - Enables leader election for high availability
- `--metrics-addr=0.0.0.0:8080` - Metrics server address

### Resource Limits

- **Requests:** CPU: 100m, Memory: 128Mi
- **Limits:** CPU: 500m, Memory: 512Mi

### Security Context

- **Run as non-root:** `runAsUser: 1000`
- **No privilege escalation:** `allowPrivilegeEscalation: false`
- **Drop all capabilities:** Security hardened
- **Read-only root filesystem:** `false` (may need to write temporary files)

### Ports

- **8080:** Metrics endpoint (TCP)
- **9443:** Webhook endpoint (TCP)

### RBAC Permissions

The deployment includes ClusterRole with permissions for:
- Core Kubernetes resources (pods, services, configmaps, secrets, events)
- Cert-manager resources (certificates, certificaterequests, issuers, clusterissuers)
- Step-issuer CRDs (stepissuers, stepclusterissuers)
- Leader election (leases in coordination.k8s.io)

## 🔍 Troubleshooting

### Common Issues

**Pod not starting / RunContainerError / CrashLoopBackOff**
```bash
# Get the pod name
POD_NAME=$(kubectl get pods -n step-issuer -l app=step-issuer -o jsonpath='{.items[0].metadata.name}')

# Check pod description for exact error
kubectl describe pod -n step-issuer $POD_NAME | grep -A 10 "Last State\|State\|Message\|Error"

# Check previous container logs (if container crashed)
kubectl logs -n step-issuer $POD_NAME --previous

# Check current logs
kubectl logs -n step-issuer $POD_NAME
```

## 🧹 Cleanup

```bash
# Delete all resources
kubectl delete -f deployment.yaml

# Or delete individually
kubectl delete deployment step-issuer -n step-issuer
kubectl delete service step-issuer -n step-issuer
kubectl delete serviceaccount step-issuer -n step-issuer
kubectl delete clusterrole step-issuer
kubectl delete clusterrolebinding step-issuer
kubectl delete namespace step-issuer

```

## 📚 Use Cases

This Kubernetes deployment is perfect for:

- **Certificate Management:** Issue TLS certificates using Step Certificates CA
- **cert-manager Integration:** Work with cert-manager to automate certificate provisioning
- **PKI Operations:** Manage StepIssuer and StepClusterIssuer resources
- **CI/CD Integration:** Automated certificate provisioning in pipelines
- **Development Testing:** Test Step Issuer functionality in Kubernetes


## ✅ Verification Checklist

After deployment, verify:

- [ ] Pod is running (`kubectl get pods -n step-issuer`)
- [ ] Pod logs show "successfully acquired lease"
- [ ] All controllers started (StepIssuer, StepClusterIssuer, CertificateRequest)
- [ ] Service endpoints are configured
- [ ] CRDs are installed and accessible
- [ ] RBAC permissions are correct
- [ ] Metrics endpoint is accessible (port 8080)
- [ ] No permission errors in logs

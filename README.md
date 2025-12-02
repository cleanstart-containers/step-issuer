# 🔐 CleanStart Container for Step Issuer

## Overview

Step Issuer is a cert-manager issuer that integrates with Step Certificates to provide automated TLS certificate management in Kubernetes. It allows you to use Step Certificates as a Certificate Authority (CA) for issuing certificates through cert-manager's standard Certificate and CertificateRequest resources.

**Key Features:**
* Kubernetes-native certificate issuer for cert-manager
* Integration with Step Certificates CA
* Automated TLS certificate provisioning
* Support for StepIssuer and StepClusterIssuer resources
* Security-hardened base image with minimal attack surface
* Non-root execution for enhanced security

**Common Use Cases:**
* Automated TLS certificate management in Kubernetes
* Integration of Step Certificates with cert-manager
* PKI operations in cloud-native environments
* Certificate provisioning for microservices
* Development and testing of certificate workflows

## Image Details

**Image:** `cleanstart/step-issuer:latest-dev`

**Key Specifications:**
* **Binary Location:** `manager`
* **User:** `clnstrt` (non-root, UID 1000)
* **Architecture:** `amd64`
* **OS:** `linux`
* **SSL Certificates:** Pre-configured at `/etc/ssl/certs/ca-certificates.crt`

## Pull Commands

Download the runtime container images:

```bash
docker pull cleanstart/step-issuer:latest-dev
```

## Kubernetes Deployment

The `kubernetes/` directory contains a complete, production-ready Kubernetes deployment:

**Files:**
* `deployment.yaml` - Complete deployment manifest including:
  - Namespace
  - ServiceAccount
  - ClusterRole and ClusterRoleBinding (RBAC)
  - Deployment with proper security context
  - Service for metrics and webhook endpoints
* `README.md` - Comprehensive deployment guide with:
  - Step-by-step installation instructions
  - Prerequisites and requirements
  - Testing procedures
  - Troubleshooting guide
  - Configuration details


## Configuration

### Command Arguments

The Step Issuer controller supports the following arguments:

* `--enable-leader-election` - Enables leader election for high availability
* `--metrics-addr=0.0.0.0:8080` - Metrics server address and port

### Environment Variables

* `SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt` - SSL certificate path

### Ports

* **8080** - Metrics endpoint (TCP)
* **9443** - Webhook endpoint (TCP)

## Best Practices

* Use specific image tags for production (avoid `latest-dev` in production)
* Configure resource limits: CPU and memory constraints
* Enable leader election for high availability deployments
* Monitor metrics endpoint for observability
* Use RBAC with least privilege principle
* Keep cert-manager and Step Issuer CRDs up to date

## Architecture Support

**Multi-Platform Images**

```bash
docker pull --platform linux/amd64 cleanstart/step-issuer:latest-dev
docker pull --platform linux/arm64 cleanstart/step-issuer:latest-dev
```

## Security Notes

* The container runs as non-root user (`clnstrt`, UID 1000)
* All capabilities are dropped for security
* RBAC is configured with least privilege principle
* SSL certificates are pre-configured for secure communication
* Leader election ensures only one instance is active at a time


# 🔐 CleanStart Container for Step Issuer

Step Issuer is a cert-manager issuer that integrates with Step Certificates to provide automated TLS certificate management in Kubernetes. It allows you to use Step Certificates as a Certificate Authority (CA) for issuing certificates through cert-manager's standard Certificate and CertificateRequest resources. The CleanStart Step-Issuer image provides a production-ready, security-hardened container optimized for enterprise environments. Built on a minimal base OS with comprehensive security hardening, this image delivers reliable application execution with advanced security features.

**📌 CleanStart Foundation:** Security-hardened, minimal base OS designed for enterprise containerized environments.

**Image Path:** `cleanstart/step-issuer`

**Registry:** CleanStart Registry

---

## Overview

Step Issuer is a Kubernetes-native certificate issuer that integrates with Step Certificates CA for automated TLS certificate management. It operates as a cert-manager controller, continuously monitoring Certificate and CertificateRequest resources and coordinating with Step Certificates to issue and manage certificates. This CleanStart Step-Issuer container is part of the CleanStart application suite, featuring enterprise-grade security hardening, automated vulnerability management, and compliance with industry standards.

---

## About CleanStart

CleanStart is a comprehensive container registry providing security-hardened, enterprise-ready container images. Our images are designed with security-first principles, featuring minimal attack surfaces, regular security updates, and compliance with industry standards.

### About CleanStart Images

CleanStart images are built on secure, minimal base operating systems and optimized for production environments. Each image undergoes rigorous security testing, vulnerability scanning, and compliance validation to ensure enterprise-grade security and reliability.

---

## Image Details

**Image:** `cleanstart/step-issuer:latest-dev`

**Key Specifications:**
- **Binary Location:** `manager`
- **User:** `clnstrt` (non-root, UID 1000)
- **Architecture:** `amd64`
- **OS:** `linux`
- **SSL Certificates:** Pre-configured at `/etc/ssl/certs/ca-certificates.crt`

---

## Key Features

- **Security-First Design**: Built with minimal attack surfaces and security hardening
- **Enterprise Compliance**: Meets industry standards including FIPS, STIG, and CIS benchmarks
- **Regular Updates**: Automated security patches and vulnerability management
- **Multi-Architecture Support**: Available for AMD64 and ARM64 architectures
- **Production Ready**: Optimized for enterprise deployment and scaling
- **Comprehensive Documentation**: Detailed guides and best practices for each image
- Kubernetes-native certificate issuer for cert-manager
- Integration with Step Certificates CA
- Automated TLS certificate provisioning
- Support for StepIssuer and StepClusterIssuer resources
- Security-hardened base image with minimal attack surface
- Non-root execution for enhanced security

---

## Common Use Cases

Typical scenarios where this container excels:

- Automated TLS certificate management in Kubernetes
- Integration of Step Certificates with cert-manager
- PKI operations in cloud-native environments
- Certificate provisioning for microservices
- Development and testing of certificate workflows
- Internal CA management for enterprise applications
- Zero-trust security implementations

---

## Quick Start

### Pull Commands

Download the runtime container images:
```bash
docker pull cleanstart/step-issuer:latest-dev
```

### Run Commands

Basic test:
```bash
docker run -it --name step-issuer-test cleanstart/step-issuer:latest-dev
```

Production deployment:
```bash
docker run -d --name step-issuer-prod \
  --read-only \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  cleanstart/step-issuer:latest
```

---

## Kubernetes Deployment

The `kubernetes/` directory contains a complete, production-ready Kubernetes deployment:

### Files

- `deployment.yaml` - Complete deployment manifest including:
  - Namespace
  - ServiceAccount
  - ClusterRole and ClusterRoleBinding (RBAC)
  - Deployment with proper security context
  - Service for metrics and webhook endpoints
- `README.md` - Comprehensive deployment guide with:
  - Step-by-step installation instructions
  - Prerequisites and requirements
  - Testing procedures
  - Troubleshooting guide
  - Configuration details

---

## Configuration

### Command Arguments

The Step Issuer controller supports the following arguments:

- `--enable-leader-election` - Enables leader election for high availability
- `--metrics-addr=0.0.0.0:8080` - Metrics server address and port

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `SSL_CERT_FILE` | `/etc/ssl/certs/ca-certificates.crt` | SSL certificate path |

### Ports

- **8080** - Metrics endpoint (TCP)
- **9443** - Webhook endpoint (TCP)

---

## Best Practices

- Use specific image tags for production (avoid `latest-dev` in production)
- Configure resource limits: CPU and memory constraints
- Enable leader election for high availability deployments
- Monitor metrics endpoint for observability
- Use RBAC with least privilege principle
- Keep cert-manager and Step Issuer CRDs up to date
- Implement proper monitoring and alerting
- Use secure Step Certificates CA configuration

---

## Architecture Support

CleanStart images support multiple architectures to ensure compatibility across different deployment environments:

- **AMD64**: Intel and AMD x86-64 processors
- **ARM64**: ARM-based processors including Apple Silicon and ARM servers

### Multi-Platform Images
```bash
docker pull --platform linux/amd64 cleanstart/step-issuer:latest-dev
docker pull --platform linux/arm64 cleanstart/step-issuer:latest-dev
```

---

## Security Notes

The container runs with multiple security enhancements:

- The container runs as non-root user (`clnstrt`, UID 1000)
- All capabilities are dropped for security
- RBAC is configured with least privilege principle
- SSL certificates are pre-configured for secure communication
- Leader election ensures only one instance is active at a time

---

## Resources

- **Official Documentation:** https://github.com/smallstep/step-issuer
- **Step Certificates Documentation:** https://smallstep.com/docs/step-ca/
- **Provenance / SBOM / Signature:** https://images.cleanstart.com/images/step-issuer
- **Docker Hub:** https://hub.docker.com/r/cleanstart/step-issuer
- **CleanStart All Images:** https://images.cleanstart.com
- **CleanStart Community Images:** https://hub.docker.com/u/cleanstart


---

## Vulnerability Disclaimer

CleanStart offers Docker images that include third-party open-source libraries and packages maintained by independent contributors. While CleanStart maintains these images and applies industry-standard security practices, it cannot guarantee the security or integrity of upstream components beyond its control.

Users acknowledge and agree that open-source software may contain undiscovered vulnerabilities or introduce new risks through updates. CleanStart shall not be liable for security issues originating from third-party libraries, including but not limited to zero-day exploits, supply chain attacks, or contributor-introduced risks.

**Security remains a shared responsibility:** CleanStart provides updated images and guidance where possible, while users are responsible for evaluating deployments and implementing appropriate controls.

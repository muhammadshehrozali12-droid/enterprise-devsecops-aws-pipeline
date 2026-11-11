# Enterprise DevSecOps AWS Pipeline

An enterprise-grade, secure, and production-ready cloud infrastructure and automated CI/CD pipeline built around Zero Trust architecture principles.

## 🚀 Key Architecture & Components

1. **Infrastructure as Code (Terraform):**
   - Custom VPC with public, private app, and private database subnets across 2 Availability Zones.
   - Managed AWS EKS cluster with Bottlerocket OS nodes in private subnets, strict IMDSv2 enforcement, and Customer-Managed KMS encryption.
   - AWS Secrets Manager integration via External Secrets Operator (ESO) and IAM Roles for Service Accounts (IRSA).

2. **Container Security & Hardening (`app/Dockerfile`):**
   - Multi-stage build with hash-pinned dependencies (`--require-hashes`).
   - Runtime base image: Distroless Python 3.13 (`nonroot` user, UID 65532).
   - Read-only root filesystem with explicit memory-backed `/tmp` paths.

3. **Automated DevSecOps Pipeline (`.github/workflows/devsecops.yml`):**
   - **Security Gates:** Unit Tests, Bandit (SAST), Safety & pip-audit (SCA), and Checkov (IaC/Config scan).
   - **Container Security:** Trivy scan failing on HIGH/CRITICAL vulnerabilities, plus CycloneDX SBOM generation.
   - **Supply Chain Security:** Keyless signing using Cosign and secure AWS ECR publishing via GitHub OIDC.

4. **Network Security & Kubernetes (`k8s/`):**
   - **Gateway API:** Modern Envoy Gateway behind an AWS NLB for secure TLS termination.
   - **NetworkPolicy:** Default-deny posture allowing ingress strictly from Envoy proxy pods and egress only to CoreDNS, AWS PrivateLink endpoints, and DB subnets.
   - **Pod Security Admission:** Enforced `restricted` baseline standard.
     

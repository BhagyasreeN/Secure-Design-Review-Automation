---
name: infra-security
description: "Review infrastructure-as-code files (Terraform, CloudFormation, Dockerfiles, Kubernetes manifests, Helm charts) for security misconfigurations. Checks for overly permissive IAM, public resources, missing encryption, insecure defaults, and CIS benchmark violations. Use when the user asks about infrastructure security, Terraform security, IaC review, Docker hardening, Kubernetes security, or cloud security review."
compatibility: Works with Terraform, CloudFormation, Docker, Kubernetes, and Helm files. No external tools required.
metadata:
  version: "1.0.0"
---

# Infrastructure Security — IaC Review

AI-driven development is accelerating infrastructure changes just as much
as application code. A misconfigured security group or an overly permissive
IAM policy can expose an entire environment in minutes. Reviewing IaC early
— before it reaches a deployment pipeline — is essential.

Review infrastructure-as-code for security misconfigurations that could
expose resources, data, or credentials.

## Step 1: Discover IaC Files

```bash
find . -maxdepth 5 \( \
  -name "*.tf" -o \
  -name "*.tfvars" -o \
  -name "Dockerfile*" -o \
  -name "docker-compose*.yml" -o \
  -name "docker-compose*.yaml" -o \
  -name "*.template" -o \
  -name "cloudformation*.json" -o \
  -name "cloudformation*.yaml" -o \
  -name "*.k8s.yml" -o \
  -name "*.k8s.yaml" -o \
  -name "deployment*.yaml" -o \
  -name "service*.yaml" -o \
  -name "values.yaml" -o \
  -name "Chart.yaml" -o \
  -name "kustomization.yaml" -o \
  -name "jules.yml" \
\) -not -path "*/.git/*" -not -path "*/node_modules/*" \
   -not -path "*/.terraform/*"
```

Also check for Terraform directories:
```bash
find . -maxdepth 3 -type d -name "terraform" -o -name "infra" -o -name "infrastructure"
```

**If no IaC files are found**, inform the user:

> No infrastructure-as-code files found in this project. If your IaC files
> are in a different location, please specify the path.

## Step 2: Review by Category

### 2a. Terraform / OpenTofu

| Check | What to look for |
|-------|-----------------|
| **IAM Policies** | `"Action": "*"`, `"Resource": "*"`, `"Effect": "Allow"` without conditions |
| **Network** | `0.0.0.0/0` in ingress/egress, public subnets for private resources |
| **Encryption** | Missing `encrypted = true`, no KMS key specified, unencrypted EBS/S3/RDS |
| **Public Access** | `publicly_accessible = true`, public S3 bucket policies, public IPs on internal services |
| **Logging** | Missing CloudTrail, VPC Flow Logs, S3 access logging |
| **State** | Local state files (should use remote backend), unencrypted state |
| **Secrets** | Hardcoded values in `.tf` or `.tfvars` files |
| **Provider** | Missing version constraints, insecure provider sources |

**Critical Terraform patterns:**
```hcl
# BAD: Wildcard IAM
resource "aws_iam_policy" "bad" {
  policy = jsonencode({
    Statement = [{ Action = "*", Resource = "*", Effect = "Allow" }]
  })
}

# BAD: Public S3
resource "aws_s3_bucket_public_access_block" "bad" {
  block_public_acls       = false
  block_public_policy     = false
}

# BAD: Open security group
resource "aws_security_group_rule" "bad" {
  cidr_blocks = ["0.0.0.0/0"]
  from_port   = 0
  to_port     = 65535
}
```

### 2b. Dockerfiles

| Check | What to look for |
|-------|-----------------|
| **Base image** | Using `latest` tag, unversioned base, non-official images |
| **Root user** | Missing `USER` directive (runs as root by default) |
| **Secrets** | `COPY` of `.env`, key files; `ARG`/`ENV` with secrets |
| **Packages** | Installing unnecessary packages, not cleaning apt cache |
| **Ports** | Exposing unnecessary ports |
| **COPY** | `COPY . .` without `.dockerignore` (may include secrets) |
| **Multi-stage** | Missing multi-stage build (build tools in production image) |
| **Health** | Missing `HEALTHCHECK` directive |

**Critical Dockerfile patterns:**
```dockerfile
# BAD: Running as root
FROM node:18
COPY . .
RUN npm install
CMD ["node", "server.js"]
# Missing: USER node

# BAD: Secrets in build
ARG DB_PASSWORD
ENV DB_PASSWORD=${DB_PASSWORD}

# BAD: Copying everything
COPY . .
# Without .dockerignore, may include .env, .git, keys
```

### 2c. Kubernetes Manifests

| Check | What to look for |
|-------|-----------------|
| **Privileged** | `privileged: true`, `hostNetwork: true`, `hostPID: true` |
| **Root** | `runAsUser: 0`, missing `runAsNonRoot: true` |
| **Capabilities** | Unnecessary Linux capabilities, missing `drop: ALL` |
| **Resources** | Missing resource limits (CPU/memory) |
| **Secrets** | Plain text secrets in manifests (use Sealed Secrets or Vault) |
| **RBAC** | `ClusterRole` with `*` verbs or resources |
| **Network** | Missing `NetworkPolicy`, services exposed as `LoadBalancer` without need |
| **Read-only** | Missing `readOnlyRootFilesystem: true` |
| **Service accounts** | `automountServiceAccountToken: true` when not needed |

### 2d. CloudFormation

| Check | What to look for |
|-------|-----------------|
| **IAM** | Wildcard actions/resources, missing conditions |
| **Security Groups** | `0.0.0.0/0` ingress on non-public ports |
| **Encryption** | Missing `StorageEncrypted`, `KmsKeyId`, `SSEAlgorithm` |
| **Public** | Public RDS, public Elasticsearch, public Redshift |
| **Logging** | Missing CloudWatch Logs, missing access logging |
| **Parameters** | Secrets in `Default` values, `NoEcho` not set for passwords |

## Step 3: Severity Classification

| Severity | Criteria |
|----------|----------|
| **CRITICAL** | Direct public exposure of data/services, wildcard admin IAM, hardcoded secrets |
| **HIGH** | Overly permissive network rules, missing encryption, running as root in production |
| **MEDIUM** | Missing logging, loose version pins, unnecessary capabilities |
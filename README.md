# 📝 GreenAssist Infrastructure Platform
**Production-Grade 3-Tier EKS Deployment with CI/CD & Service Mesh**

---

## 🏗️ Architecture Overview

```
User ──► Route 53 ──► CloudFront ──► WAF 
                                       │
         ┌─────────────────────────────┘
         ▼
  ALB (Public Subnet) 
         │
         ▼
  EKS Cluster (Private Subnet) ──► Istio Service Mesh ──► Flask Pods (2 Replicas)
         │                                                   │
         └───────────────────► RDS / S3 ◄────────────────────┘
                               (Data Tier)
```

This is **not** a simple Flask app. It's an **Enterprise-level Architecture** that handles:
- ✅ Auto-scaling based on CPU/Memory metrics
- ✅ Zero-downtime deployments (Rolling/Canary)
- ✅ Service-to-service mTLS encryption
- ✅ Multi-AZ high availability
- ✅ Automated CI/CD pipeline
- ✅ Infrastructure as Code (IaC) best practices

---

## 🚀 Tech Stack

| Layer | Technology |
|-------|-----------|
| **Cloud Platform** | AWS (VPC, EKS, ECR, IAM, ALB, Route 53, CloudFront) |
| **Infrastructure as Code** | Terraform (Modular, State Management) |
| **Container Orchestration** | Kubernetes (Amazon EKS) |
| **CI/CD Pipeline** | GitHub Actions (Automated Docker Build & K8s Deploy) |
| **Service Mesh** | Istio (mTLS, Traffic Management, Observability) |
| **Deployment Strategy** | Argo Rollouts (Canary/Blue-Green Deployments) |
| **API Framework** | Python Flask |
| **Container Registry** | Amazon ECR (Elastic Container Registry) |
| **Secrets Management** | AWS Secrets Manager & IAM Roles (IRSA) |

---

## 🏛️ The 6 Pillars of AWS Well-Architected Framework

### 🔐 **Security**
- **3-Tier VPC Architecture**: Application and Database resources isolated in Private Subnets
- **IAM Roles with Least Privilege**: IRSA (IAM Roles for Service Accounts) enables pod-level permissions
- **Network Security**: Security Groups and NACLs restrict traffic flow
- **Service Mesh mTLS**: Istio automatically encrypts pod-to-pod communication
- **No Hardcoded Credentials**: All secrets managed via AWS Secrets Manager

### 🔄 **Reliability**
- **Multi-AZ Deployment**: EKS cluster spans multiple Availability Zones
- **Auto-Healing**: Kubernetes automatically restarts failed pods
- **Load Balancing**: ALB distributes traffic across healthy endpoints
- **Replica Strategy**: Minimum 2 pod replicas ensure high availability
- **Health Checks**: Liveness and readiness probes detect unhealthy pods

### 🎯 **Operational Excellence**
- **Infrastructure as Code**: 100% Terraform automation (no manual AWS Console changes)
- **Automated Deployments**: GitHub Actions triggers on code push
- **Centralized Logging**: Container logs streamed to CloudWatch
- **Monitoring & Alerts**: Prometheus metrics + Grafana dashboards
- **Runbooks**: Well-documented deployment procedures

### ⚡ **Performance Efficiency**
- **Global Edge Caching**: CloudFront CDN reduces latency
- **Right-Sized Instances**: t3.medium nodes optimized for workload
- **Horizontal Pod Autoscaling (HPA)**: Pods scale based on CPU/Memory metrics
- **Vertical Pod Autoscaling**: Resource requests/limits optimized for efficiency
- **Container Optimization**: Multi-stage Docker builds reduce image size

### 💰 **Cost Optimization**
- **ECR Lifecycle Policies**: Old container images auto-deleted (retention: 30 days)
- **Spot Instances**: Non-critical workloads run on AWS Spot for 70% savings
- **RDS Reserved Instances**: Predictable database costs
- **Right-Sizing**: Storage and compute resources matched to actual usage
- **Cost Monitoring**: AWS Cost Explorer + Athena for FinOps

### 🌱 **Sustainability**
- **Optimized Container Resources**: Pod CPU/RAM limits prevent resource waste
- **Efficient Scheduling**: Kubernetes bin-packing minimizes node count
- **Auto-Scaling Down**: Cluster scales to zero during off-peak hours
- **Lightweight Base Images**: Alpine Linux reduces carbon footprint

---

## 📂 Project Structure

```
.
├── app.py                           # Flask Application Logic
├── Dockerfile                       # Multi-stage optimized Docker build
├── requirements.txt                 # Python Dependencies
├── .gitignore                       # Git ignore rules
│
├── k8s/                             # Kubernetes Manifests
│   ├── deployment.yaml              # Flask Deployment (2 replicas) + Service
│   ├── resource-quota.yaml          # Namespace resource limits
│   └── network-policy.yaml          # Pod-to-pod network policies
│
├── terraform/                       # Modular Infrastructure as Code
│   ├── main.tf                      # Root configuration (Provider, Modules)
│   ├── outputs.tf                   # Output values (Cluster Endpoint, etc.)
│   ├── variables.tf                 # Input variables (Region, CIDR blocks, etc.)
│   ├── terraform.tfvars             # Terraform variables (sensitive data excluded)
│   │
│   └── modules/
│       ├── vpc/                     # Network Foundation (Public/Private Subnets)
│       │   ├── main.tf
│       │   ├── outputs.tf
│       │   └── variables.tf
│       │
│       ├── eks/                     # Compute Layer (EKS Cluster + Node Groups)
│       │   ├── main.tf
│       │   ├── outputs.tf
│       │   └── variables.tf
│       │
│       ├── rds/                     # Data Tier (PostgreSQL Database)
│       │   ├── main.tf
│       │   ├── outputs.tf
│       │   └── variables.tf
│       │
│       └── k8s-addons/              # Kubernetes Add-ons (Istio, Argo, ALB Controller)
│           ├── main.tf
│           └── variables.tf
│
└── .github/
    └── workflows/
        ├── docker-build.yaml        # Build & Push Docker image to ECR
        ├── k8s-deploy.yaml          # Update K8s manifests & Deploy
        └── terraform-deploy.yaml    # Validate & Apply Terraform changes

```

---

## 🛠️ Getting Started

### **Prerequisites**
```bash
# Required Tools
- Terraform >= 1.5.0
- AWS CLI v2
- kubectl >= v1.27
- Docker (for local testing)
- Git
```

### **1️⃣ Provision Infrastructure**

```bash
# Clone the repository
git clone https://github.com/yourusername/greenassist-platform.git
cd greenassist-platform/terraform

# Initialize Terraform (downloads provider plugins)
terraform init

# Plan the infrastructure
terraform plan -out=tfplan

# Apply the infrastructure (creates AWS resources)
terraform apply tfplan

# Wait ~15-20 minutes for EKS cluster to be ready
```

### **2️⃣ Configure kubectl Access**

```bash
# Update kubeconfig with EKS cluster credentials
aws eks update-kubeconfig --region us-east-1 --name greenassist-eks

# Verify cluster connectivity
kubectl get nodes
kubectl get namespaces
```

### **3️⃣ Deploy Flask Application**

```bash
# Create namespace
kubectl create namespace flask-app

# Deploy resources
kubectl apply -f k8s/deployment.yaml -n flask-app

# Monitor pods
kubectl get pods -n flask-app
kubectl logs -f -n flask-app -l app=flask-app
```

### **4️⃣ Access the Application**

```bash
# Get LoadBalancer External IP
kubectl get svc -n flask-app flask-app-svc

# Test the application
curl http://<EXTERNAL-IP>
```

---

## 🚀 CI/CD Pipeline

The GitHub Actions pipeline automates the entire deployment workflow:

### **Workflow Steps**

```
Code Push to main
       │
       ▼
1. Run Tests (pytest, linting)
       │
       ▼
2. Build Docker Image (Multi-stage)
       │
       ▼
3. Scan for Vulnerabilities (Trivy)
       │
       ▼
4. Push to Amazon ECR
       │
       ▼
5. Update K8s Manifests (sed + IMAGE_TAG)
       │
       ▼
6. Deploy to EKS (kubectl apply)
       │
       ▼
7. Verify Health (rollout status)
       │
       ▼
✅ Zero-Downtime Deployment Complete!
```

### **Manual Deployment**

```bash
# Build Docker image locally
docker build -t greenassist:latest .

# Tag for ECR
docker tag greenassist:latest <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/greenassist:latest

# Push to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/greenassist:latest

# Update deployment
kubectl set image deployment/flask-app flask-app=<AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/greenassist:latest -n flask-app
```

---

## 🧪 Testing & Validation

### **Local Testing**

```bash
# Build and run Flask app locally
docker build -t flask-app:test .
docker run -p 5000:5000 flask-app:test

# Test endpoints
curl http://localhost:5000/health
curl http://localhost:5000/api/status
```

### **Kubernetes Testing**

```bash
# Check pod status
kubectl describe pod <POD_NAME> -n flask-app

# Stream logs
kubectl logs -f <POD_NAME> -n flask-app

# Execute commands inside pod
kubectl exec -it <POD_NAME> -n flask-app -- /bin/sh

# Port-forward to local pod
kubectl port-forward svc/flask-app-svc 5000:80 -n flask-app
curl http://localhost:5000
```

### **Infrastructure Validation**

```bash
# Validate Terraform
terraform validate
terraform fmt -check

# Security scanning
tfsec terraform/

# Policy as Code
conftest test -p policy/ terraform/
```

---

## 📊 Monitoring & Observability

### **CloudWatch Dashboard**

```bash
# View container logs
aws logs tail /aws/eks/greenassist-eks --follow

# Monitor resource metrics
aws cloudwatch get-metric-statistics --namespace AWS/EKS \
  --metric-name ClusterCpuUtilization \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-01-01T01:00:00Z \
  --period 300 --statistics Average
```

### **Istio Observability**

```bash
# Access Kiali dashboard (Service Mesh visualization)
kubectl port-forward svc/kiali 20000:20000 -n istio-system
# Open: http://localhost:20000

# Access Grafana dashboards
kubectl port-forward svc/grafana 3000:3000 -n istio-system
# Open: http://localhost:3000
```

---

## 🔧 Troubleshooting

### **Pods Not Starting**

```bash
# Check pod events
kubectl describe pod <POD_NAME> -n flask-app

# Check resource requests/limits
kubectl top pods -n flask-app

# Check node resources
kubectl top nodes
```

### **Terraform Apply Failed**

```bash
# Check AWS credentials
aws sts get-caller-identity

# Enable debug logging
export TF_LOG=DEBUG
terraform apply

# Validate state
terraform state list
terraform state show module.eks
```

### **LoadBalancer Not Reachable**

```bash
# Check service endpoints
kubectl get endpoints -n flask-app

# Verify security groups
aws ec2 describe-security-groups --filters Name=tag:kubernetes.io/cluster/greenassist-eks,Values=owned

# Check ALB target health
aws elbv2 describe-target-health --target-group-arn <TARGET_GROUP_ARN>
```

---

## 👨‍🏫 Mentor's Perspective (For Interviewers)

### **What is this project?**
This is a **complete Modern DevOps lifecycle demonstration**. It showcases how code travels from a developer's laptop → GitHub → Docker container → Amazon ECR → Kubernetes cluster, **entirely automated** without manual intervention.

### **Why is it important in real companies?**
Enterprise applications like **GreenAssist** (waste management platform) must maintain **99.99% uptime**. This architecture ensures:
- Zero-downtime deployments during updates
- Automatic failover if infrastructure fails
- Secure communication between services
- Cost-efficient resource utilization
- Audit trail of all infrastructure changes

### **Common Mistakes Beginners Make (That I Avoided!)**

| Mistake | Impact | My Solution |
|---------|--------|------------|
| 🚫 Putting everything in public subnet | **Security breach risk** | ✅ 3-tier VPC with private subnets |
| 🚫 Hardcoding AWS credentials | **Credential leak in GitHub** | ✅ IAM Roles + IRSA (no credentials) |
| 🚫 Manual deployments | **Human errors, inconsistency** | ✅ Fully automated CI/CD pipeline |
| 🚫 Single pod deployment | **Service downtime if pod fails** | ✅ Multi-replica with auto-healing |
| 🚫 Monolithic Terraform files | **Unreadable, hard to maintain** | ✅ Modular architecture with modules/ |

---

## 📊 Real-World Analogy

### **This project is like an Automated Restaurant:**

- 🏗️ **Terraform** = The construction crew that built the kitchen (AWS infrastructure)
- 👨‍🍳 **EKS** = Head chef managing multiple stoves (Pods cooking dishes simultaneously)
- 🤖 **GitHub Actions** = Automated order system that brings fresh recipes whenever they update
- 📦 **ECR** = Pantry storing pre-made meals (Docker images)
- 🔗 **Istio Service Mesh** = Communication lines ensuring chefs talk securely
- 📊 **Prometheus/Grafana** = Quality control checks monitoring taste and presentation
- 🔄 **Argo Rollouts** = Smart serving system that gradually switches from old menu to new menu

---

## 🏆 Production Checklist

Before deploying to production, ensure:

- [ ] **Security**: All pods running with non-root users
- [ ] **Secrets**: No hardcoded credentials in images/manifests
- [ ] **Scaling**: HPA configured with appropriate thresholds
- [ ] **Backups**: RDS automated backups enabled (retention: 30 days)
- [ ] **Monitoring**: CloudWatch alarms set for critical metrics
- [ ] **Logging**: Centralized logging configured (ELK/CloudWatch)
- [ ] **Disaster Recovery**: Multi-AZ failover tested
- [ ] **Cost**: Budget alerts configured in AWS Billing
- [ ] **Compliance**: Security/audit requirements met
- [ ] **Documentation**: Runbooks for common incidents

---

## 📚 Resources

- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [EKS Best Practices Guide](https://aws.github.io/aws-eks-best-practices/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest)
- [Istio Service Mesh](https://istio.io/latest/docs/)
- [Argo Rollouts](https://argoproj.github.io/argo-rollouts/)

---

## 📞 Support & Contact

- **Issues**: Open GitHub Issues for bugs/feature requests
- **Discussions**: Use GitHub Discussions for Q&A
- **Email**: your-email@example.com

---

## 📜 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

Built following AWS Well-Architected principles and Kubernetes best practices for enterprise-grade deployments.

**Last Updated**: March 7, 2026

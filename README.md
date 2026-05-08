# 🚀 AWS EKS Cluster with Terraform (IAM Roles + Addons)

This repository provisions a fully managed **Amazon EKS Kubernetes Cluster** using Terraform, including:

- VPC + Networking (pre-requisite assumed in separate module)
- EKS Cluster
- Managed Node Group IAM Role
- Required IAM Policies
- OIDC Provider for IRSA
- EBS CSI Driver Addon

---

## 📌 Architecture Overview

This setup creates:

- **EKS Cluster Role**
- **EKS Worker Node Role**
- **Amazon EBS CSI Driver Role (IRSA)**
- **OIDC Provider for IAM Roles for Service Accounts**
- **EKS Addon (EBS CSI Driver)**

---

## 📁 Project Structure


.
├── main.tf
├── iam.tf
├── eks.tf
├── addons.tf
├── variables.tf
├── outputs.tf
└── README.md


---

## ⚙️ Prerequisites

Before running Terraform, ensure:

- AWS CLI configured
- Terraform installed (>= 1.3)
- SSH Key pair exists in AWS (for EC2 node access)
- Proper IAM permissions for EKS provisioning

---

## 🔐 IAM Roles Created

### 1️⃣ EKS Cluster Role

Used by AWS EKS control plane:

- `AmazonEKSClusterPolicy`

```hcl
resource "aws_iam_role" "sai01_cluster_role"
2️⃣ EKS Node Group Role

Used by worker nodes (EC2 instances):

Attached policies:

AmazonEKSWorkerNodePolicy
AmazonEKS_CNI_Policy
AmazonEC2ContainerRegistryReadOnly
AmazonEBSCSIDriverPolicy
3️⃣ EBS CSI Driver Role (IRSA)

Used for Kubernetes persistent storage:

Uses OIDC Federation
Attached to aws-ebs-csi-driver
🔌 EKS Addons
📦 AWS EBS CSI Driver

Enables dynamic EBS volume provisioning for Kubernetes PVCs.

resource "aws_eks_addon" "ebs_csi_driver"

Features:

Version pinned: v1.58.0-eksbuild.1
Conflict resolution: OVERWRITE
Uses IRSA IAM Role
🔐 OIDC Provider (IRSA)

Enables secure IAM roles for Kubernetes service accounts:

resource "aws_iam_openid_connect_provider" "eks"

Used by:

EBS CSI Driver
Future Kubernetes workloads
🧾 Variables
variable "ec2_key" {
  description = "SSH key pair name for EC2 nodes"
  type        = string
  default     = "ec2-key"
}
🚀 Deployment Steps
1️⃣ Initialize Terraform
terraform init
2️⃣ Validate configuration
terraform validate
3️⃣ Plan infrastructure
terraform plan
4️⃣ Apply infrastructure
terraform apply -auto-approve
🧹 Destroy Infrastructure

To remove all resources:

terraform destroy -auto-approve
⚠️ Important Notes
Ensure OIDC provider is created before EBS CSI addon
IAM roles must exist before EKS cluster/node creation
Avoid hardcoding AWS account IDs (use data.aws_caller_identity in production)
EBS CSI driver requires proper IRSA setup
📊 Output Example

After successful deployment:

EKS Cluster created
Node group ready
kubectl access enabled
EBS CSI storage class available
🛠️ Troubleshooting
❌ Issue: Addon fails

Check:

OIDC provider exists
IAM role trust relationship correct
❌ Issue: Nodes not joining cluster

Check:

Node IAM role permissions
Security group rules
Subnet configuration
📚 References
https://docs.aws.amazon.com/eks/latest/userguide/eks-add-ons.html
https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html
https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eks_cluster
👨‍💻 Author

Sai Kubernetes Infra Setup
Terraform | AWS EKS | DevOps | Kubernetes

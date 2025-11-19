# Deploy EKS Cluster Using Terraform

> Step-by-step README for this repository — explains what each file does and how to run it to provision an EKS cluster on AWS using Terraform.

---

## Table of contents
1. Project overview  
2. Repository layout  
3. Prerequisites  
4. Quick start (step-by-step)  
5. Detailed explanation of files  
6. Inputs (variables)  
7. Outputs  
8. Modules  
9. Common commands  
10. Destroy / cleanup  
11. Troubleshooting  
12. Security & cost notes

---

## 1) Project overview
This repository contains Terraform configuration to deploy a fully functional **AWS EKS Cluster** along with VPC, subnets, IAM roles, and worker (managed) node groups.

---

## 2) Repository layout
```
/ (repo root)
├─ main.tf        # root Terraform config that calls modules and providers
├─ variables.tf   # variable declarations
├─ outputs.tf     # outputs after apply
├─ terraform.tfvars (optional) # user-provided variables
└─ modules/       # VPC, EKS, Node groups, IAM modules
```

---

## 3) Prerequisites
- AWS account with admin or required IAM permissions
- Terraform v1.3+ installed
- AWS CLI configured (`aws configure`)
- kubectl installed

---

## 4) Quick start — step-by-step
### 1. Clone repo
```bash
git clone https://github.com/bhupal2027/Deploy_EKS_Cluster_Using_Terraform.git
cd Deploy_EKS_Cluster_Using_Terraform
```

### 2. Create terraform.tfvars
Example:
```hcl
aws_region = "us-east-1"
cluster_name = "my-eks-cluster"
vpc_cidr = "10.0.0.0/16"
public_subnets  = ["10.0.0.0/24", "10.0.1.0/24"]
private_subnets = ["10.0.2.0/24", "10.0.3.0/24"]
node_group_desired_capacity = 2
node_instance_type = "t3.medium"
```

### 3. Initialize Terraform
```bash
terraform init
```

### 4. Validate & plan
```bash
terraform validate
terraform plan -var-file="terraform.tfvars"
```

### 5. Apply
```bash
terraform apply -var-file="terraform.tfvars"
```

### 6. Configure kubectl
```bash
aws eks --region <region> update-kubeconfig --name <cluster_name>
kubectl get nodes
```

---

## 5) Detailed explanation of root files
### main.tf
- Configures AWS provider
- Calls modules (VPC, EKS, nodes)

### variables.tf
- All inputs required by modules

### outputs.tf
- Cluster endpoint, CA, node role ARNs, VPC info, kubeconfig instructions

### modules/
- Contains isolated reusable Terraform modules such as:
  - `vpc/`
  - `eks/`
  - `node_group/`
  - `iam/`

---

## 6) Inputs (Variables)
Common variables include:
- `aws_region`
- `cluster_name`
- `vpc_cidr`
- `public_subnets`
- `private_subnets`
- `node_instance_type`
- `node_group_desired_capacity`
- `ssh_key_name` (optional)

Use `terraform.tfvars` for setting values.

---

## 7) Outputs
After apply you typically get:
- EKS cluster name
- API endpoint
- CA certificate
- VPC ID, subnet IDs
- Node group IAM role
- Instructions for kubeconfig

---

## 8) Modules
### VPC Module
Creates VPC, subnets, route tables, NAT, IGW, security groups.

### EKS Module
Creates EKS control plane, IAM roles, cluster security groups.

### Node Group Module
Creates managed node groups, IAM role, scaling config.

---

## 9) Common Terraform Commands
```bash
terraform fmt
terraform validate
terraform plan
terraform apply
terraform destroy
```

Update kubeconfig:
```bash
aws eks --region <region> update-kubeconfig --name <cluster_name>
```

---

## 10) Cleanup / Destroy
To avoid AWS costs destroy the infrastructure:
```bash
terraform destroy -var-file="terraform.tfvars"
```

---

## 11) Troubleshooting
- **kubectl can't connect** → run update-kubeconfig again
- **Nodes not joining** → check `aws-auth` ConfigMap
- **IAM role errors** → ensure AWS user has correct permissions
- **Apply stuck** → verify VPC, EC2, ENI quotas

---

## 12) Security & Cost Notes
- EKS control plane + EC2 nodes incur hourly cost
- Never commit AWS credentials or terraform.tfstate
- Use S3 backend + DynamoDB lock for team projects

---

If you want, I can add a **backend.tf**, **GitHub Actions CI/CD**, or **architecture diagram** section.


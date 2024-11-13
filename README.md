# AWS EKS Infrastructure with Terraform

This repository contains Terraform configurations to deploy a production-ready Amazon Elastic Kubernetes Service (EKS) cluster with associated networking infrastructure.

## 🏗 Infrastructure Overview

The configuration creates the following AWS resources:
- VPC with 2 public subnets across different availability zones
- Internet Gateway for public internet access
- EKS cluster with managed node groups
- Associated security groups and IAM roles
- Route tables for network traffic management

### Network Architecture
```
VPC (10.0.0.0/16)
├── Subnet 1 (us-east-1a)
│   └── Public subnet with auto-assigned public IPs
├── Subnet 2 (us-east-1b)
│   └── Public subnet with auto-assigned public IPs
└── Internet Gateway
    └── Route table for internet access
```

## 🚀 Prerequisites

- [Terraform](https://www.terraform.io/downloads.html) (v1.0.0+)
- [AWS CLI](https://aws.amazon.com/cli/) configured with appropriate credentials
- [kubectl](https://kubernetes.io/docs/tasks/tools/) for cluster management
- An SSH key pair in your AWS account

## 📋 Configuration Components

### VPC Configuration
- CIDR Block: 10.0.0.0/16
- Two public subnets in different availability zones
- Internet Gateway for public internet access
- Route tables for subnet traffic management

### EKS Cluster
- Kubernetes control plane managed by AWS
- Deployed across multiple availability zones
- Integrated with VPC and security groups

### Node Groups
- Instance Type: t2.medium
- Auto-scaling configuration:
  - Minimum: 1 node
  - Maximum: 2 nodes
  - Desired: 2 nodes
- Remote SSH access enabled

### Security Groups
1. Cluster Security Group:
   - Allows all outbound traffic
   - Controlled inbound access

2. Node Security Group:
   - Allows all inbound and outbound traffic
   - Should be customized based on security requirements

### IAM Roles and Policies
1. Cluster Role:
   - AmazonEKSClusterPolicy
   - AmazonEKSVPCResourceController

2. Node Group Role:
   - AmazonEKSWorkerNodePolicy
   - AmazonEKS_CNI_Policy
   - AmazonEC2ContainerRegistryReadOnly

## 🛠 Usage

### 1. Initialize Terraform
```bash
terraform init
```

### 2. Review the deployment plan
```bash
terraform plan -var="ssh_key_name=your-key-name"
```

### 3. Apply the configuration
```bash
terraform apply -var="ssh_key_name=your-key-name"
```

### 4. Configure kubectl
After successful deployment, configure kubectl to interact with your cluster:
```bash
aws eks update-kubeconfig --name devsecops-cluster --region us-east-1
```

## 📌 Variables

| Name | Description | Type | Default |
|------|-------------|------|---------|
| ssh_key_name | SSH key pair name for EC2 instances | string | "aws" |

## 📤 Outputs

| Name | Description |
|------|-------------|
| cluster_id | The ID of the EKS cluster |
| node_group_id | The ID of the EKS node group |
| vpc_id | The ID of the created VPC |
| subnet_ids | The IDs of the created subnets |

## ⚠️ Security Considerations

1. The current configuration includes broad security group rules that should be tightened for production:
   - Node group security group allows all inbound traffic (0.0.0.0/0)
   - Consider limiting to specific CIDR ranges or security groups

2. SSH access is enabled for debugging purposes:
   - Consider disabling or limiting to specific IP ranges
   - Implement bastion hosts for production environments

3. Subnet Configuration:
   - Currently using public subnets
   - Consider private subnets with NAT Gateways for production

## 🧹 Cleanup

To destroy all created resources:
```bash
terraform destroy -var="ssh_key_name=your-key-name"
```

## 📝 Notes

- The configuration uses t2.medium instances to avoid vCPU limits
- Node group size is limited to 2 nodes by default
- The cluster is created in the us-east-1 region
- All resources are tagged with appropriate names for easier identification

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

# This project is licensed under the MIT License - see the LICENSE file for details.
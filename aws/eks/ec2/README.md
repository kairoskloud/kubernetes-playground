## AWS EKS EC2-based Cluster – Terraform & CLI Quickstart

This folder contains a **minimal, opinionated Terraform configuration** to create an **EKS cluster backed only by EC2 managed node groups** (no Fargate), including its own VPC with **two public and two private subnets**.

### Prerequisites

- **Terraform** >= 1.3.0
- **AWS CLI** configured with credentials and a profile
- No existing VPC required – this example **creates a dedicated VPC with 2 public and 2 private subnets** for the cluster.

---

### Input parameters

Key input variables are defined in `variables.tf`:

| **Variable**              | **Default**                         | **Description**                                                                 |
|---------------------------|-------------------------------------|---------------------------------------------------------------------------------|
| `region`                  | `us-east-1`                         | AWS region to deploy the EKS cluster into.                                      |
| `aws_profile`             | `default`                           | AWS CLI profile name used by the provider.                                      |
| `cluster_name`            | `eks-ec2-cluster`                   | Name of the EKS cluster.                                                        |
| `cluster_version`         | `1.35`                              | Kubernetes version for the EKS control plane.                                   |
| `endpoint_public_access`  | `true`                              | Whether the EKS API server is accessible from the public internet.              |
| `endpoint_private_access` | `true`                              | Whether the EKS API server is accessible from within the VPC.                   |
| `public_access_cidrs`     | `["0.0.0.0/0"]`                     | CIDR blocks allowed to access the public EKS endpoint.                          |
| `enabled_cluster_log_types` | `["api","audit","authenticator","controllerManager","scheduler"]` | Control plane log types to enable.                           |
| `vpc_cidr`                | `10.1.0.0/16`                       | CIDR block for the EKS VPC.                                                     |
| `public_subnet_cidrs`     | `["10.1.1.0/24","10.1.2.0/24"]`     | CIDR blocks for the two public subnets.                                         |
| `private_subnet_cidrs`    | `["10.1.11.0/24","10.1.12.0/24"]`   | CIDR blocks for the two private subnets used by EKS and node groups.            |
| `node_min_size`           | `3`                                 | Minimum number of nodes in the managed node group.                              |
| `node_max_size`           | `5`                                 | Maximum number of nodes in the managed node group.                              |
| `node_desired_size`       | `3`                                 | Desired number of nodes in the managed node group.                              |
| `node_instance_types`     | `["m7i-flex.large"]`                | EC2 instance types for the managed node group.                                  |
| `tags`                    | `{}`                                | Map of tags applied to supported AWS resources.                                 |

---

### 1. Set your variables

Create a `terraform.tfvars` file (recommended) in this folder:

```hcl
region       = "us-east-1"
aws_profile  = "your-profile"
cluster_name = "my-eks-ec2"

# Optional overrides
vpc_cidr             = "10.1.0.0/16"
public_subnet_cidrs  = ["10.1.1.0/24", "10.1.2.0/24"]
private_subnet_cidrs = ["10.1.11.0/24", "10.1.12.0/24"]

node_min_size     = 3
node_max_size     = 5
node_desired_size = 3
node_instance_types = ["m7i-flex.large"]

tags = {
  Environment = "dev"
  Project     = "kubernetes-playground"
}
```

---

### 2. Initialize Terraform

Run once in the `aws/ec2` directory:

```bash
terraform init
```

---

### 3. Plan the deployment

Using `terraform.tfvars`:

```bash
terraform plan
```

---

### 4. Apply to create the EKS EC2 cluster

With `terraform.tfvars`:

```bash
terraform apply
```

Confirm `yes` when prompted.

---

### 5. Configure `kubectl` for the new cluster

Use the AWS CLI to update your kubeconfig:

```bash
aws eks update-kubeconfig --name eks-ec2-cluster --region us-east-1
```

After this, `kubectl get nodes` should show the EC2 worker nodes.

---

### 6. Destroy the cluster and clean up

From the same directory:

```bash
terraform destroy
```

After the cluster is destroyed, you can also **remove the EKS context and related entries from your local kubeconfig** using `kubectl`:

```bash
# Remove the context
kubectl config delete-context my-eks-ec2

# (Optional) remove the cluster and user entries if you want a full cleanup
kubectl config delete-cluster my-eks-ec2
kubectl config unset users.my-eks-ec2
```

This cleans up the kubeconfig entries so `kubectl config get-contexts` and `kubectl config get-clusters` no longer show the deleted cluster.


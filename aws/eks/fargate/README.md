## AWS EKS Fargate Cluster – Terraform & CLI Quickstart

This folder contains a **minimal, opinionated Terraform configuration** to create an **EKS cluster with both Fargate and EC2 managed node group workers**, including its own VPC with **two public and two private subnets**.

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
| `cluster_name`            | `eks-fargate-cluster`               | Name of the EKS cluster.                                                        |
| `cluster_version`         | `1.35`                              | Kubernetes version for the EKS control plane.                                   |
| `fargate_namespace`       | `default`                           | Kubernetes namespace whose pods are scheduled onto Fargate.                     |
| `endpoint_public_access`  | `true`                              | Whether the EKS API server is accessible from the public internet.              |
| `endpoint_private_access` | `true`                              | Whether the EKS API server is accessible from within the VPC.                   |
| `public_access_cidrs`     | `["0.0.0.0/0"]`                     | CIDR blocks allowed to access the public EKS endpoint.                          |
| `enabled_cluster_log_types` | `["api","audit","authenticator","controllerManager","scheduler"]` | Control plane log types to enable.                           |
| `vpc_cidr`                | `10.0.0.0/16`                       | CIDR block for the EKS VPC.                                                     |
| `public_subnet_cidrs`     | `["10.0.1.0/24","10.0.2.0/24"]`     | CIDR blocks for the two public subnets.                                         |
| `private_subnet_cidrs`    | `["10.0.11.0/24","10.0.12.0/24"]`   | CIDR blocks for the two private subnets used by EKS, Fargate, and node groups.  |
| `node_min_size`           | `3`                                 | Minimum number of nodes in the managed node group.                              |
| `node_max_size`           | `5`                                 | Maximum number of nodes in the managed node group.                              |
| `node_desired_size`       | `3`                                 | Desired number of nodes in the managed node group.                              |
| `tags`                    | `{}`                                | Map of tags applied to supported AWS resources.                                 |

---

### 1. Set your variables

Create a `terraform.tfvars` file (recommended) in this folder:

```hcl
region       = "us-east-1"
aws_profile  = "your-profile"
cluster_name = "my-eks-fargate"

# Optional overrides (defaults are usually fine for a playground)
vpc_cidr            = "10.0.0.0/16"
public_subnet_cidrs = ["10.0.1.0/24", "10.0.2.0/24"]
private_subnet_cidrs = ["10.0.11.0/24", "10.0.12.0/24"]

node_min_size     = 3
node_max_size     = 5
node_desired_size = 3

tags = {
  Environment = "dev"
  Project     = "kubernetes-playground"
}
```

Alternatively, you can pass variables on the CLI with `-var` flags, but `tfvars` keeps commands simpler.

---

### 2. Initialize Terraform

Run once in the `aws/eks/fargate` directory:

```bash
terraform init
```

---

### 3. Plan the deployment (CLI-friendly)

Using `terraform.tfvars`:

```bash
terraform plan
```

Or passing variables explicitly from the CLI:

```bash
terraform plan
```

---

### 4. Apply to create the EKS Fargate cluster

With `terraform.tfvars`:

```bash
terraform apply
```

Or fully via CLI:

```bash
terraform apply
```

Confirm `yes` when prompted.

---

### 5. Configure `kubectl` for the new cluster

Use the AWS CLI to update your kubeconfig:

```bash
aws eks update-kubeconfig --name eks-fargate-cluster --region us-east-1
```

After this, `kubectl get nodes` should show your Fargate-backed nodes (as virtual nodes).

---

### 6. Destroy the cluster and clean up

From the same directory, either:

```bash
terraform destroy
```

or, if you prefer explicit CLI variables:

```bash
terraform destroy 
```

---

### 7. Optional: One-liner helpers (CLI shortcuts)

If you want pure CLI convenience, you can export environment variables and then run the commands more compactly:

```bash
export TF_VAR_region=us-west-2
export TF_VAR_aws_profile=your-profile
export TF_VAR_cluster_name=my-eks-fargate
export TF_VAR_subnet_ids='["subnet-xxxxxx","subnet-yyyyyy"]'

terraform init
terraform apply   # uses TF_VAR_*
```

This avoids repeating long `-var` flags and keeps the flow fully **CLI-driven**.

---

### Notes & Best Practices

- **Networking**: ensure the `subnet_ids` are in subnets with proper routing and NAT/internet access as needed for your workloads.
- **Security**: for stricter posture, consider:
  - `endpoint_public_access = false`
  - `endpoint_private_access = true`
  - Limiting `public_access_cidrs` to trusted networks.
- **State**: for real environments, configure a **remote backend** (e.g. S3 + DynamoDB) in `terraform.tf`/`provider.tf`.

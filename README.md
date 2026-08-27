<div align="center">
  <img src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/terraform/terraform.png" width="80" height="80"/>
  <h1>🌩️ AWS 3-Tier Architecture Deployment</h1>
  <p><i>Automated Infrastructure Provisioning with Terraform</i></p>
  
  [![Terraform](https://img.shields.io/badge/Terraform-1.0+-844FBA?style=for-the-badge&logo=terraform&logoColor=white)](https://www.terraform.io/)
  [![AWS](https://img.shields.io/badge/AWS-Architecture-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
</div>

---

## 🎨 Architecture Overview

This repository converts a highly available, robust manual AWS deployment into **Infrastructure as Code (IaC)**. The architecture spans across 2 Availability Zones and strictly isolates resources across Web, App, and Database tiers.

### 🌐 1. Network Tier (`modules/network`)
- **VPC:** Custom isolated environment (`192.168.0.0/16`).
- **Subnets:** 2 Public (Web) & 4 Private (App, DB).
- **Gateways:** Internet Gateway for edge routing, NAT Gateway for secure outbound access.
- **Security:** 5 distinct Security Groups enforcing **Least-Privilege**:
  - `External ALB SG` -> `Web SG` -> `Internal ALB SG` -> `App SG` -> `Database SG`.

### 🗄️ 2. Database Tier (`modules/db`)
- **Isolation:** Placed strictly in DB Private Subnets.
- **Resource:** **RDS MySQL** database instance (No Public IP).

### ⚙️ 3. Application Tier (`modules/app`)
- **Compute:** Amazon Linux 2023 running Node.js managed by `pm2`.
- **Scaling:** Target Tracking ASG (Min: 2, Max: 6) at `70% CPU`.
- **Routing:** Internal Application Load Balancer.
- **Security:** IAM Role attached for AWS SSM Session Manager (No Bastion Hosts needed).

### 🖥️ 4. Web Tier (`modules/web`)
- **Compute:** Amazon Linux 2023 running Nginx reverse proxy.
- **Routing:** External Internet-Facing Application Load Balancer.
- **Scaling:** Autoscaling Group identically matched to the App tier.

---

## 📂 Project Structure

```bash
terraform-3tier-project-new.git/
├── backend.tf            # S3 / DynamoDB State Configuration
├── main.tf               # Root Module
├── modules/
│   ├── app/              # App Tier & Internal ALB
│   ├── db/               # RDS MySQL
│   ├── network/          # VPC, Subnets, SGs
│   └── web/              # Web Tier & External ALB
├── outputs.tf            # DNS outputs
├── terraform.tfvars      # Environment Variables
└── variables.tf          # Variable Declarations
```

---

## 🚀 Deployment Instructions

### 1️⃣ Clone the Repository
```bash
git clone https://github.com/vaibhavingle2002/terraform-3tier-project-new.git
cd terraform-3tier-project-new
```

### 2️⃣ Configure
Update `terraform.tfvars` with your specific DB credentials, AMI IDs, S3 bucket names, and ACM certificates.

### 3️⃣ Initialize & Apply
```bash
terraform init
terraform plan
terraform apply --auto-approve
```

### 4️⃣ Cleanup
```bash
terraform destroy --auto-approve
```

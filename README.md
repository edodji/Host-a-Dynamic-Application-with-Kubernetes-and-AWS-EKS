# Host-a-Dynamic-Application-with-Kubernetes-and-AWS-EKS

This project demonstrates how to host a secure, scalable, and fault-tolerant dynamic web application using **Kubernetes on AWS EKS**. The solution includes complete DevOps practices from infrastructure provisioning, CI/CD containerization, to secure secret management and high availability.

---

##  Project Overview

The project includes:

- Infrastructure as Code (IaC) setup for a full VPC with public/private subnets across multiple AZs
- RDS-backed application for persistent storage
- Dockerized PHP/Apache web app built from GitHub
- Secure secret and credential management using AWS Secrets Manager and IAM roles
- Fully containerized deployment to AWS EKS with autoscaling and managed nodes
- TLS secured domain routing using Route 53 and Load Balancer

---

##  Technologies Used

- **Amazon EKS / Kubernetes**
- **Amazon RDS (Multi-AZ)**
- **Amazon ECR / Docker**
- **Secrets Manager**
- **Amazon S3**
- **IAM Roles & Policies**
- **Route 53**
- **Flyway (for DB migration)**
- **Helm, kubectl, eksctl, Chocolatey**
- **PHP + Apache**

---

##  Project Setup: Step-by-Step

###  Initial Setup

1. - Set up Git and GitHub account  
2. - Create SSH key pairs and add your public key to GitHub  
3. - Create and configure a Docker Hub account  
4. - Configure AWS CLI with IAM user credentials  

###  VPC & Network Infrastructure

5. - Created a custom **VPC**  
6. - Deployed **public and private subnets** in 2 Availability Zones  
7. - Set up **Internet Gateway**  
8. - Configured **NAT Gateways** in public subnets  
9. - Created **Security Groups** for EC2, RDS, and EKS  
10. - Used **2 AZs** for high availability and redundancy  

###  Backend: Database & S3

11. - Launched **Amazon RDS (MySQL)** with multi-AZ failover  
12. - Uploaded SQL migration script to **Amazon S3**  
13. - Created IAM role with S3 read permissions  
14. - Launched **EC2 instance** and used **EC2 Instance Connect Endpoint** for secure remote login  
15. - Migrated SQL data to RDS using **Flyway** CLI script  

###  Docker & Image Management

16. - Created and cloned a GitHub repo for app + Dockerfile  
17. - Built Docker image locally  
18. - Created **Amazon ECR** repository  
19. - Tagged and pushed the image to ECR  

###  Secrets, Roles & Access

20.  Created IAM roles for:
    - EC2 with S3 access
    - EKS control plane
    - Node groups
    - Secrets Manager access  
21.  Stored database credentials in **AWS Secrets Manager**

###  Kubernetes on EKS

22. - Installed **kubectl, eksctl, helm** via Chocolatey  
23. - Provisioned **Amazon EKS cluster**  
24. - Launched **EKS managed node group**  
25. - Created **Kubernetes namespace, service account, and deployment manifest**  
26. - Mounted Secrets via **CSI driver** inside pods  

###  Routing & Security

27. - Created **TLS listener** on NLB  
28. - Configured **Amazon Route 53** with A-record for HTTPS routing  

---

##  Key Files

###  Data Migration Script (Flyway)

Automates database migration from S3 to RDS using Flyway CLI.

```bash
# Download and run SQL migration using Flyway
aws s3 cp "$S3_URI" sql/
flyway -url=jdbc:mysql://"$RDS_ENDPOINT":3306/"$RDS_DB_NAME" \
  -user="$RDS_DB_USERNAME" \
  -password="$RDS_DB_PASSWORD" \
  -locations=filesystem:sql \
  migrate
```

---

###  Dockerfile

Installs PHP/Apache, clones app from GitHub, configures Laravel `.env`, and starts Apache.

> Includes MySQL, PHP extensions, and `.env` injection for secure RDS connection.

---

###  Docker Build Script

Builds the image with arguments for GitHub token, RDS, and app environment setup.

```bash
docker build `
  --build-arg PERSONAL_ACCESS_TOKEN=... `
  --build-arg GITHUB_USERNAME=... `
  --build-arg REPOSITORY_NAME=... `
  ...
  -t your-image-name .
```

---

###  Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rentzone-deployment
  namespace: rentzone
spec:
  replicas: 1
  template:
    spec:
      serviceAccountName: rentzone-service-account
      containers:
      - name: rentzone
        image: <your-ecr-image-uri>
        volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
```

---

##  Features

-  Secure secret management via Secrets Manager
-  Scalable Kubernetes deployments on EKS
-  TLS-secured routing with Route 53 + Load Balancer
-  Multi-AZ redundancy with public/private subnet segregation
-  Infrastructure automation ready (IAC & CI/CD integration)
-  Database migration with Flyway & RDS

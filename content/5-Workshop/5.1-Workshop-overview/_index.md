---
title: "Workshop Overview"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### System Architecture

The DaiVietBlood system uses a **Serverless-First** architecture on AWS Cloud, prioritizing scalability, security, and operational optimization.

![AWS architecture picture](../../images/5-Workshop/AWS_Architecture.jpg)

#### Architecture Components

**1. Network Infrastructure (VPC)**

| Component | Description |
|:----------|:------------|
| **VPC** | Virtual Private Cloud with CIDR 10.0.0.0/16 |
| **Public Subnet** | Contains NAT Gateway, allows Internet access |
| **Private Subnet** | Contains Lambda, RDS - isolated from Internet |
| **NAT Gateway** | Allows Private Subnet resources to access Internet |
| **Internet Gateway** | Allows Public Subnet to communicate with Internet |

**2. Application & Data Layer**

| Service | Role |
|:--------|:-----|
| **AWS Lambda** | Process business logic (CRUD operations, emergency requests) |
| **API Gateway** | Receive HTTP requests, route to Lambda |
| **Amazon RDS** | MySQL database storing user data, blood inventory |
| **Amazon S3** | Store static files (images, documents) |

**3. Frontend & Distribution**

| Service | Role |
|:--------|:-----|
| **AWS Amplify** | Host React application |
| **CloudFront** | CDN distributes content globally with low latency |

**4. DevOps & Monitoring**

| Service | Role |
|:--------|:-----|
| **CodePipeline** | Automate CI/CD process |
| **CodeBuild** | Build and test source code |
| **CloudWatch** | Collect logs, metrics, set up alarms |

#### Data Flow

```
User Request → CloudFront → Amplify (Frontend)
                              ↓
                         API Gateway
                              ↓
                         AWS Lambda (Private Subnet)
                              ↓
                         Amazon RDS (Private Subnet)
```

#### Security Model

1. **Network Isolation**: RDS and Lambda in Private Subnet, no direct Internet access
2. **IAM Roles**: Each service has minimum required permissions (Least Privilege)
3. **Data Encryption**: At-rest (RDS, S3) and In-transit (HTTPS)
4. **Security Groups**: Control inbound/outbound traffic for each resource

#### Workshop Objectives

After completing this workshop, you will be able to:

- ✅ Create VPC with proper network segmentation
- ✅ Deploy RDS MySQL in Private Subnet
- ✅ Build Lambda functions and expose via API Gateway
- ✅ Configure S3 and CloudFront for static content
- ✅ Deploy React app with Amplify
- ✅ Set up CI/CD pipeline
- ✅ Monitor with CloudWatch

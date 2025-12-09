---
title: "Workshop"
date: "2025-09-08"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

# Building Serverless System on AWS - DaiVietBlood

#### Overview

This workshop guides you through building a **Serverless Blood Donation & Emergency System (DaiVietBlood)** on AWS. You will learn how to set up and configure the core AWS services used in the project architecture.

![AWS architecture picture](/images/AWS_Architecture.png)

#### AWS Services Used

| Service | Purpose |
|:--------|:--------|
| **Amazon VPC** | Create virtual private network with Public/Private Subnets |
| **NAT Gateway** | Allow resources in Private Subnet to access Internet |
| **Amazon RDS** | MySQL database for the application |
| **AWS Lambda** | Serverless business logic processing |
| **Amazon API Gateway** | Manage and expose REST APIs |
| **Amazon S3** | Store static assets (images, files) |
| **Amazon CloudFront** | CDN for global content distribution |
| **AWS Amplify** | Host Frontend application (React) |
| **AWS CodePipeline** | CI/CD automation |
| **Amazon CloudWatch** | Monitoring and logging |

#### What You Will Learn

- Design and deploy **Serverless-First** architecture on AWS
- Configure **VPC** with Public/Private Subnets for security
- Create **RDS MySQL** in Private Subnet
- Build **Lambda Functions** and connect with **API Gateway**
- Store and distribute content with **S3** and **CloudFront**
- Deploy React application with **AWS Amplify**
- Set up automated **CI/CD Pipeline**
- Monitor application with **CloudWatch**

#### Prerequisites

- AWS Account with Administrator access
- Basic knowledge of AWS services
- Familiarity with Node.js and React
- AWS CLI installed and configured

#### Estimated Cost

This workshop uses resources within **AWS Free Tier** when possible. Estimated cost is approximately **~$15-20** if completed within 1-2 days and resources are cleaned up immediately after.

#### Content

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Preparation](5.2-Prerequiste/)
3. [VPC & Amazon RDS](5.3-S3-vpc/)
4. [Lambda & API Gateway](5.4-S3-onprem/)
5. [S3, CloudFront & Amplify](5.5-Policy/)
6. [CI/CD, CloudWatch & Cleanup](5.6-Cleanup/)

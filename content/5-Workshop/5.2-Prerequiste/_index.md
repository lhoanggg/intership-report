---
title: "Preparation"
date: "2025-09-08"
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Prerequisites

Before starting this workshop, ensure you have:

**1. AWS Account**
- Active AWS Account with Administrator access
- Recommended: Use IAM User instead of Root account
- Region: **Asia Pacific (Singapore) - ap-southeast-1**

**2. Local Development Tools**

| Tool | Version | Purpose |
|:-----|:--------|:--------|
| Node.js | >= 18.x | Run Lambda functions locally |
| npm/yarn | Latest | Package management |
| AWS CLI | >= 2.x | Interact with AWS services |
| Git | Latest | Version control |

**3. Knowledge Requirements**
- Basic understanding of AWS services (VPC, EC2, S3)
- Familiarity with REST APIs
- Basic Node.js/JavaScript or Python
- Basic React knowledge

---

#### Step 1: Configure AWS CLI

1. Install AWS CLI from [AWS CLI Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

2. Configure credentials:
```bash
aws configure
```

3. Enter your credentials:
```
AWS Access Key ID: [Your Access Key]
AWS Secret Access Key: [Your Secret Key]
Default region name: ap-southeast-1
Default output format: json
```

4. Verify configuration:
```bash
aws sts get-caller-identity
```

---

#### Step 2: Create IAM User for Workshop

1. Go to **IAM Console** → **Users** → **Create user**

2. User details:
   - User name: `workshop-admin`
   - Select: **Provide user access to the AWS Management Console**

3. Set permissions:
   - Select: **Attach policies directly**
   - Search and select: `AdministratorAccess`

4. Create user and save credentials securely

{{% notice warning %}}
⚠️ **Security Note:** After completing the workshop, delete this IAM user or remove AdministratorAccess policy.
{{% /notice %}}

---

#### Step 3: Verify Service Quotas

Ensure your account has sufficient quotas for:

| Service | Resource | Minimum Required |
|:--------|:---------|:-----------------|
| VPC | VPCs per Region | 1 |
| VPC | Subnets per VPC | 4 |
| VPC | NAT Gateways per AZ | 1 |
| RDS | DB Instances | 1 |
| Lambda | Concurrent Executions | 10 |
| API Gateway | REST APIs | 1 |
| S3 | Buckets | 2 |

Check quotas at: **Service Quotas Console** → Select service → View quotas

---

#### Step 4: Prepare Source Code

1. Clone the sample repository:
```bash
git clone https://github.com/your-repo/daivietblood-workshop.git
cd daivietblood-workshop
```

2. Project structure:
```
daivietblood-workshop/
├── frontend/           # React application
│   ├── src/
│   └── package.json
├── backend/            # Lambda functions
│   ├── functions/
│   └── package.json
├── infrastructure/     # CloudFormation templates
│   └── templates/
└── README.md
```

3. Install dependencies:
```bash
# Frontend
cd frontend && npm install

# Backend
cd ../backend && npm install
```

---

#### Step 5: Cost Estimation

| Service | Configuration | Est. Cost/Day |
|:--------|:--------------|:--------------|
| NAT Gateway | 1 NAT Gateway | ~$1.08 |
| RDS | db.t3.micro | ~$0.52 |
| Lambda | Free Tier | $0.00 |
| API Gateway | Free Tier | $0.00 |
| S3 | < 5GB | ~$0.01 |
| CloudFront | < 1GB transfer | ~$0.01 |
| Amplify | Build & Host | ~$0.50 |

**Total estimated: ~$2-3/day**

{{% notice tip %}}
💡 **Tip:** Complete the workshop in 1-2 days and clean up resources immediately to minimize costs.
{{% /notice %}}

---

#### Checklist Before Starting

- [ ] AWS Account ready with Administrator access
- [ ] AWS CLI installed and configured
- [ ] Node.js >= 18.x installed
- [ ] Git installed
- [ ] Source code cloned
- [ ] Region set to ap-southeast-1

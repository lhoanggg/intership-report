---
title: "CI/CD, CloudWatch & Cleanup"
date: "2025-09-08"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

In this final section, you will set up CI/CD Pipeline, configure CloudWatch monitoring, and clean up all resources after completing the workshop.

---

## Part 1: CI/CD Pipeline with CodePipeline

#### Step 1: Create CodeBuild Project

1. Go to **CodeBuild Console** → **Create build project**

2. Project configuration:
   - **Project name**: `daivietblood-backend-build`
   - **Description**: Build project for Lambda functions

3. Source:
   - **Source provider**: GitHub
   - **Repository**: Select your repository
   - **Branch**: main

4. Environment:
   - **Environment image**: Managed image
   - **Operating system**: Amazon Linux 2
   - **Runtime**: Standard
   - **Image**: aws/codebuild/amazonlinux2-x86_64-standard:4.0
   - **Service role**: New service role

5. Buildspec:
   - **Build specifications**: Use a buildspec file
   - Create `buildspec.yml` file in your repository:

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 18
    commands:
      - echo Installing dependencies...
      - cd backend && npm ci
  
  pre_build:
    commands:
      - echo Running tests...
      - npm test || true
  
  build:
    commands:
      - echo Building Lambda packages...
      - mkdir -p dist
      - zip -r dist/get-users.zip functions/get-users/
      - zip -r dist/create-user.zip functions/create-user/
      - zip -r dist/emergency-requests.zip functions/emergency-requests/
  
  post_build:
    commands:
      - echo Updating Lambda functions...
      - aws lambda update-function-code --function-name daivietblood-get-users --zip-file fileb://dist/get-users.zip
      - aws lambda update-function-code --function-name daivietblood-create-user --zip-file fileb://dist/create-user.zip
      - aws lambda update-function-code --function-name daivietblood-emergency-requests --zip-file fileb://dist/emergency-requests.zip

artifacts:
  files:
    - dist/**/*
```

6. Click **Create build project**

---

#### Step 2: Create CodePipeline

1. Go to **CodePipeline Console** → **Create pipeline**

2. Pipeline settings:
   - **Pipeline name**: `daivietblood-pipeline`
   - **Service role**: New service role

3. Source stage:
   - **Source provider**: GitHub (Version 2)
   - **Connection**: Create new connection or select existing
   - **Repository name**: Select your repository
   - **Branch name**: main
   - **Output artifact format**: CodePipeline default

4. Build stage:
   - **Build provider**: AWS CodeBuild
   - **Project name**: `daivietblood-backend-build`

5. Deploy stage:
   - **Skip deploy stage** (Lambda is updated in build stage)

6. Click **Create pipeline**

---

#### Step 3: Add IAM Permissions for CodeBuild

1. Go to **IAM Console** → **Roles**
2. Find role `codebuild-daivietblood-backend-build-service-role`
3. Add inline policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "lambda:UpdateFunctionCode",
                "lambda:GetFunction"
            ],
            "Resource": "arn:aws:lambda:ap-southeast-1:*:function:daivietblood-*"
        }
    ]
}
```

---

## Part 2: CloudWatch Monitoring

#### Step 1: Create CloudWatch Dashboard

1. Go to **CloudWatch Console** → **Dashboards** → **Create dashboard**

2. Dashboard name: `DaiVietBlood-Monitoring`

3. Add widgets:

**Widget 1: Lambda Invocations**
- Widget type: Line
- Metrics: Lambda → By Function Name → Invocations
- Select all daivietblood functions

**Widget 2: Lambda Errors**
- Widget type: Number
- Metrics: Lambda → By Function Name → Errors
- Statistic: Sum

**Widget 3: Lambda Duration**
- Widget type: Line
- Metrics: Lambda → By Function Name → Duration
- Statistic: Average

**Widget 4: API Gateway Requests**
- Widget type: Line
- Metrics: ApiGateway → By Api Name → Count

**Widget 5: RDS Connections**
- Widget type: Line
- Metrics: RDS → Per-Database Metrics → DatabaseConnections

---

#### Step 2: Create CloudWatch Alarms

**Alarm 1: Lambda Errors**

1. Go to **CloudWatch** → **Alarms** → **Create alarm**
2. Select metric: Lambda → By Function Name → Errors
3. Conditions:
   - Threshold type: Static
   - Whenever Errors is: Greater than 5
   - Period: 5 minutes
4. Notification:
   - Create new SNS topic: `daivietblood-alerts`
   - Email: your-email@example.com
5. Alarm name: `DaiVietBlood-Lambda-Errors`

**Alarm 2: RDS CPU High**

1. Create alarm
2. Select metric: RDS → Per-Database Metrics → CPUUtilization
3. Conditions:
   - Threshold: Greater than 80%
   - Period: 5 minutes
4. Notification: Use existing SNS topic
5. Alarm name: `DaiVietBlood-RDS-CPU-High`

**Alarm 3: API Gateway 5XX Errors**

1. Create alarm
2. Select metric: ApiGateway → By Api Name → 5XXError
3. Conditions:
   - Threshold: Greater than 10
   - Period: 5 minutes
4. Alarm name: `DaiVietBlood-API-5XX-Errors`

---

#### Step 3: Configure Log Insights

1. Go to **CloudWatch** → **Logs** → **Logs Insights**

2. Select log groups:
   - `/aws/lambda/daivietblood-get-users`
   - `/aws/lambda/daivietblood-create-user`
   - `/aws/lambda/daivietblood-emergency-requests`

3. Sample query - Find errors:
```
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 50
```

4. Sample query - Duration statistics:
```
fields @timestamp, @duration
| stats avg(@duration), max(@duration), min(@duration) by bin(1h)
```

---

## Part 3: Resource Cleanup

{{% notice warning %}}
⚠️ **Important:** Follow these steps to avoid unexpected charges.
{{% /notice %}}

#### Cleanup Order (Important!)

Clean up in the following order to avoid dependency errors:

#### Step 1: Delete Amplify App

1. Go to **Amplify Console**
2. Select `daivietblood-frontend`
3. **Actions** → **Delete app**
4. Confirm deletion

#### Step 2: Delete CloudFront Distribution

1. Go to **CloudFront Console**
2. Select distribution → **Disable**
3. Wait for status to change to "Deployed"
4. Select distribution → **Delete**

#### Step 3: Delete S3 Buckets

1. Go to **S3 Console**
2. Select bucket `daivietblood-assets-*`
3. **Empty** bucket first
4. Then **Delete** bucket

#### Step 4: Delete API Gateway

1. Go to **API Gateway Console**
2. Select `daivietblood-api`
3. **Actions** → **Delete**

#### Step 5: Delete Lambda Functions

1. Go to **Lambda Console**
2. Delete each function:
   - `daivietblood-get-users`
   - `daivietblood-create-user`
   - `daivietblood-emergency-requests`
3. Delete Lambda Layer: `mysql2-layer`

#### Step 6: Delete RDS Instance

1. Go to **RDS Console** → **Databases**
2. Select `daivietblood-db`
3. **Actions** → **Delete**
4. Uncheck "Create final snapshot"
5. Check "I acknowledge..."
6. Type `delete me` to confirm

#### Step 7: Delete VPC Resources

1. Go to **VPC Console**

2. Delete NAT Gateway:
   - **NAT Gateways** → Select NAT Gateway → **Delete**
   - Wait for status "Deleted"

3. Release Elastic IP:
   - **Elastic IPs** → Select EIP → **Release**

4. Delete VPC Endpoints (if any):
   - **Endpoints** → Select endpoints → **Delete**

5. Delete Security Groups (except default):
   - **Security Groups** → Delete `daivietblood-lambda-sg`, `daivietblood-rds-sg`

6. Delete DB Subnet Group:
   - **RDS Console** → **Subnet groups** → Delete `daivietblood-db-subnet-group`

7. Delete VPC:
   - **Your VPCs** → Select `daivietblood-vpc` → **Delete VPC**
   - This will delete subnets, route tables, internet gateway

#### Step 8: Delete CI/CD Resources

1. **CodePipeline Console** → Delete `daivietblood-pipeline`
2. **CodeBuild Console** → Delete `daivietblood-backend-build`

#### Step 9: Delete CloudWatch Resources

1. **CloudWatch** → **Dashboards** → Delete `DaiVietBlood-Monitoring`
2. **CloudWatch** → **Alarms** → Delete all related alarms
3. **CloudWatch** → **Log groups** → Delete log groups `/aws/lambda/daivietblood-*`

#### Step 10: Delete IAM Resources

1. **IAM Console** → **Roles**
2. Delete roles:
   - `daivietblood-lambda-role`
   - `codebuild-daivietblood-*`
   - `codepipeline-daivietblood-*`

---

#### Cleanup Checklist

- [ ] Amplify app deleted
- [ ] CloudFront distribution deleted
- [ ] S3 buckets emptied and deleted
- [ ] API Gateway deleted
- [ ] Lambda functions and layers deleted
- [ ] RDS instance deleted
- [ ] NAT Gateway deleted
- [ ] Elastic IP released
- [ ] VPC and all components deleted
- [ ] CodePipeline and CodeBuild deleted
- [ ] CloudWatch dashboards, alarms, log groups deleted
- [ ] IAM roles deleted

---

#### Verify No Remaining Charges

1. Go to **AWS Cost Explorer**
2. Verify no resources are running
3. Go to **Billing Console** → **Bills** to confirm

{{% notice tip %}}
💡 **Tip:** Set up Budget Alert in AWS Budgets to receive notifications when costs exceed threshold.
{{% /notice %}}

---

## Workshop Conclusion

Congratulations! 🎉 You have completed the workshop on building a Serverless system on AWS.

#### What You Learned:

1. ✅ Design and deploy VPC with Public/Private Subnets
2. ✅ Create RDS MySQL in a secure environment
3. ✅ Build Lambda functions and expose via API Gateway
4. ✅ Configure S3 and CloudFront for static assets
5. ✅ Deploy React app with AWS Amplify
6. ✅ Set up automated CI/CD Pipeline
7. ✅ Monitor application with CloudWatch

#### Next Steps:

- Learn more about AWS Well-Architected Framework
- Explore advanced features like X-Ray tracing
- Experiment with Aurora Serverless for database
- Implement authentication with Amazon Cognito

---
title: "Event 5"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 4.5. </b> "
---



# Summary Report: Attending AWS Cloud Mastery Series #2 – DevOps on AWS

### Attendance Objectives

Continuing the event series, I attended the second session focusing on **DevOps**. My goal was to master AWS services supporting DevOps, deepen my understanding of CI/CD pipeline design, Infrastructure as Code (IaC) concepts, as well as how to deploy and monitor containerized applications on the AWS platform.

### Speakers

The session gathered a strong lineup of speakers, including AWS Community Builders and experienced engineers:

- Mr. Truong Quang Tinh – Platform Engineer (TymeX)
- Mr. Bao Huynh, Nguyen Khanh Phuc Thinh, Tran Dai Vi, Huynh Hoang Long, Pham Hoang Quy, Nghiem Le (AWS Community Builders)
- Mr. Dinh Le Hoang Anh – Cloud Engineer Trainee (First Cloud AI Journey)

### Valuable Knowledge I Harvested:

**1. Building a DevOps Foundation**
The speakers helped me redefine that DevOps is not a job title but a **mindset and habit** of working. The core lies in:

- Automating repetitive tasks.
- Sharing knowledge across teams.
- Continuously experimenting and learning.
- Measuring effectiveness with real data rather than assumptions.

I also learned lessons about common pitfalls for beginners: avoid getting stuck in "tutorial hell" without starting real projects, and focus on personal progress rather than comparing oneself to others.

**2. Infrastructure as Code (IaC) in Practice**
This section broadened my horizon regarding IaC tools instead of sticking to a single one. The speakers provided a detailed comparison:

- **CloudFormation:** AWS's native template tool.
- **AWS CDK:** For developers who prefer writing infrastructure using familiar programming languages.
- **Terraform:** The optimal choice for teams working on multi-cloud platforms.

The most important message: Infrastructure built via Code (IaC) is significantly more consistent, maintainable, and secure compared to manual configuration (ClickOps).

**3. Containers and Deployment Models**
The content ranged from basics (Dockerfile, Image) to advanced AWS services:

- **Amazon ECR:** Storage and security scanning for images.
- **Amazon ECS & EKS:** Two popular container orchestration options. The comparison between ECS (simple, native) and EKS (powerful with Kubernetes) helped me know when to use which.
- **AWS App Runner:** A quick deployment solution without worrying about cluster management.

**4. Monitoring and Observability**
A system cannot lack monitoring capabilities. I understood better the roles of:

- **Amazon CloudWatch:** The center for metrics, logs, and alarms.
- **AWS X-Ray:** A tracing tool helping visualize request flows and detect bottlenecks.

Core lesson: Observability features must be designed **from the very beginning**, not added after the system is built.

### Plan for Application at Work

Specifically, I plan to apply this knowledge to the team's upcoming **AI Chatbot Project**:

- **Establish CI/CD Pipeline:** Use **AWS CodePipeline** to automate the entire process from Build, Test, to Deploy. The goal is to ensure every code update is tested and pushed to production smoothly.
- **Implement IaC:** Use **AWS CDK** to define all resources (Lambda, API Gateway, DynamoDB, S3, IAM...). This makes the system easy to reuse for different environments and scale quickly when needed.

Applying this process will help the Chatbot project develop faster, minimize human error, and make operations easier.

**Event Experience**
The session gave me a practical perspective on how modern businesses implement DevOps on AWS. Beyond theory, the real-world examples from speakers about CloudFormation, Terraform, or how to choose EKS/ECS were truly valuable.

Beyond professional knowledge, this was also a good occasion for me to network with like-minded friends and learn "hard-earned" experiences from those who came before me.

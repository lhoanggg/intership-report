Here is the English version of the report, maintaining the structure and formatting suitable for your AWS program submission.

---

title: "Event 6"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 4.6. </b> "

---



# Event Summary Report: AWS Cloud Mastery Series #3

**Topic:** AWS Well-Architected – Security Pillar Workshop

## 1. Overview & Speakers

The event focused on the most critical pillar within the AWS Well-Architected Framework: **Security**. The content was designed to equip attendees with knowledge ranging from fundamental to advanced levels regarding identity, monitoring, infrastructure protection, data protection, and incident response processes.

**Speakers & Experts:**
The event gathered experts from the AWS Community (AWS Community Builders), AWS Cloud Club Captains from various universities (HCMUTE, SGU, PTIT, HUFLIT), Cloud Engineers from FCJ, and specially featuring **Mendel Grabski** (Security & DevOps Expert).

**About AWS Cloud Club:**
This is a network connecting students and professionals, helping to develop technical leadership skills, providing hands-on experiences, and offering long-term mentoring opportunities. The participating Cloud Clubs under FCJA include: HCMUTE, SGU, PTIT, and HUFLIT.

## 2. Key Technical Highlights

### A. Identity & Access Management (IAM)

IAM is defined as the "first line of defense." The session emphasized the shift from manual management to automation and strict adherence to key principles:

- **Least Privilege Principle:** Grant only the necessary permissions required to perform a task.
- **Root User Protection:** Delete access keys immediately after creation.
- **Service Control Policies (SCPs):** Use Organization-level policies to set a "ceiling" (maximum available permissions) for member accounts (Note: SCPs only filter permissions; they do not grant them).
- **Permission Boundaries:** Set the maximum permissions that an identity-based policy can grant to a specific User/Role.
- **Multi-Factor Authentication (MFA):** Encouraged the use of FIDO2 (hardware keys/biometrics) over traditional TOTP.
- **Credential Rotation:** Use **AWS Secrets Manager** to automate the rotation process (create -> set -> test -> finish) and integrate with EventBridge to manage schedules, eliminating risks associated with "hardcoded credentials."

### B. Continuous Monitoring & Threat Detection

The focus was on building comprehensive visibility and automated response capabilities:

- **Multi-Layer Monitoring:** Combining CloudTrail (recording API calls, S3/Lambda access) and VPC Flow Logs (network traffic).
- **Event-Driven Security:** Using **EventBridge** as a central event bus to route real-time alerts to Lambda/SNS/SQS or coordinate actions across different accounts (Cross-account routing).
- **Detection-as-Code:** Managing detection rules and queries (CloudTrail Lake queries) as code (version control) to ensure consistent deployment across the organization.

**Deep Dive into Amazon GuardDuty:**
This is an intelligent threat detection solution that operates continuously based on three main data sources: CloudTrail, VPC Flow Logs, and DNS Logs.

- **Expanded Coverage:** Protection for S3, EKS, RDS (brute-force detection), Lambda (suspicious network activity), and Malware Protection (EBS scanning).
- **Runtime Monitoring:** Uses an Agent to monitor deep inside the OS (processes, file access) on EC2/EKS/Fargate.

### C. Compliance & Infrastructure as Code (IaC)

Security compliance is no longer a manual check but is integrated into the deployment pipeline:

- **Applied Standards:** AWS Foundational Security Best Practices, CIS Benchmark, PCI DSS, NIST.
- **Enforcement Mechanism:** Using **AWS CloudFormation** (IaC) to deploy standard configurations, combined with **AWS Security Hub** to automatically audit resources against defined standards.

### D. Network & Data Protection

- **Network Security:** Clearly distinguishing between Security Groups (Stateful - instance level firewall) and NACLs (Stateless - subnet level firewall). Introduction to AWS Network Firewall for advanced Egress filtering/IPS and integration with Threat Intelligence to automatically block malicious traffic.
- **Data Protection:**
  - **Encryption:** Using KMS with Customer Master Keys (CMK) and Policy conditions to control decryption contexts.
  - **Certificates:** Using AWS ACM to manage and automatically renew SSL/TLS certificates.
  - **Service Security:** Enforcing HTTPS/TLS 1.2+ for S3 (via Bucket Policy) and Databases (e.g., PostgreSQL `rds.force_ssl=1`).

### E. Incident Response (IR)

The standard IR process consists of 5 steps: **Preparation -> Detection & Analysis -> Containment -> Eradication & Recovery -> Post-Incident Activity**.

- **Prevention Strategy:** Never make S3 buckets public, isolate sensitive services in private subnets, and ensure all infrastructure changes go through IaC with a review process (double-gate).

## 3. Practical Experience & Q&A

The event provided high practical value, specifically aligning with the **"Automated Incident Response and Forensics"** project our team is developing.

**Discussion Point:**
During our project testing, the team noticed that **Amazon GuardDuty** has a latency of about 5 minutes to generate a finding after an incident occurs. We asked about solutions to reduce this latency.

**Expert Answer:**

- **The Nature of the Service:** The GuardDuty latency is an accepted technical characteristic because the system needs time to analyze large datasets to accurately determine threats and avoid false positives.
- **Alternative Solutions:** To achieve near real-time detection, the expert suggested integrating 3rd party solutions like **OpenClarity** (open source) or building custom anomaly detection logic based on CloudTrail events.

**Networking:**
After the event, Mr. **Mendel Grabski** (Ex-Head of Security & DevOps) expressed interest and offered professional support for our project, opening up valuable collaboration and mentoring opportunities.

#### Some photos from the event participation

- _[Add your photos here]_
  > Overall, the event not only provided technical knowledge but also changed my mindset regarding application design, system modernization, and effective team collaboration.

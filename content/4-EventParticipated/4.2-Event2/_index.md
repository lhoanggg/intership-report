---
title: "Event 2"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---



# Data Resiliency in a Cloud-first World

### Event Objectives

- Share best practices in modern application design.
- Introduce DDD methods and event-driven architecture.
- Guide on selecting appropriate compute services.
- Introduce AI tools supporting the development lifecycle.

### List of Speakers

- **Paul Haverfield** -
  Principal Storage Specialist BDM, APJ
- **Tamelly Lim** - Specialist Solutions Architect
- **Ameen Khan S** - GTM specialist for Storage - Data & AI pillar covering ASEAN markets
- **Paul Hidalgo** -

### Key Highlights

Today, I had the opportunity to attend an AWS program covering an extremely urgent topic in the current context: **Data Resiliency**. More than just simple backups, the event opened up new perspectives on protecting digital assets against increasingly sophisticated threats.

Here are the key takeaways I gathered:

### 1. Redefining: How is Data Resiliency different from High Availability (HA) and Disaster Recovery (DR)?

Previously, we often focused on HA (ensuring systems are always online) or DR (recovering from physical disasters). However, **Data Resiliency** is a broader and more "proactive" concept:

- **Context:** "Everything fails, all the time" (Werner Vogels) – Everything is prone to failure, including physical keys or hardware.
- **The Difference:** While HA handles infrastructure incidents, Data Resiliency focuses on **data integrity**. It is the organizational ability to maintain operations, withstand, and recover even when under cyberattack (such as Ransomware) or human error.
- **Goal:** To detect anomalies and automate response processes without human intervention.

### 2. Why has Data Resiliency become an "Absolute Necessity"?

The explosion of data creation comes with new technological vulnerabilities. Three main trends are driving the shift from _Protection_ to _Resiliency_:

1.  **Regulatory:** Compliance with strict data protection laws.
2.  **Technology:** The complexity of Multi-cloud and Hybrid-cloud environments.
3.  **Threat Landscape:** Ransomware no longer just encrypts primary data; it also targets backups.

### 3. Data Immutability: The Impenetrable Shield

A keyword mentioned repeatedly was **Data Immutability**.

- This refers to the ability to create data copies that **cannot be changed or deleted** for a set period.
- In the event of a Ransomware attack, even if hackers possess top-level admin rights, they cannot alter this backup.
- It acts as the "Last line of defense," ensuring that at least one clean version always exists for recovery.

### 4. Protection Strategy: The AWS 3-2-1-1-0 Model

The traditional 3-2-1 backup rule has been upgraded to suit the cloud era:

- **3** copies of data.
- **2** different storage media.
- **1** off-site copy (different region).
- **1** offline or **Immutable** (Air-gapped) copy.
- **0** errors during recovery (verified by automated testing).

Important concepts to remember:

- **RPO (Recovery Point Objective):** How much data loss is acceptable?
- **RTO (Recovery Time Objective):** How long does it take to get the system running again?
- **Backup Vault:** A container for storing backups, encrypted by AWS KMS for enhanced security.

### 5. Tool Ecosystem & Solutions

The event introduced powerful integrated solutions on AWS:

- **Commvault Cloud on AWS:**
  - Provides _Air Gap Protect_ (secure data isolation).
  - _Cloud Rewind:_ The ability to "rewind" time to restore entire instances or VPCs as if the incident never happened.
- **Clumio:**
  - An all-in-one simplified backup solution.
  - Uses _Serverless Workflow_ architecture (an army of Lambda functions) to optimize costs and operations.
- **Elastio:**
  - Focuses on: Detect, Respond, Recover.
  - Scans for Malware/Ransomware directly within Snapshots to ensure backups do not contain latent malicious code.

### 6. Workshop Architecture: Real-world Implementation

During the hands-on session, we deployed a comprehensive protection model:

**Key Components:**

- **Source:** EC2 Instances (EBS) and S3 Buckets containing critical data.
- **Mechanism:** Using **AWS Backup Plan** with an hourly schedule.
- **Protection Layers:**
  - _Primary:_ Stored in a standard Vault (`workshop-sources-regular-vault`).
  - _Secondary (Air-gapped):_ Copied to another Region (`us-east-1-LAG-Vault`) with **Immutability** settings enabled.
- **Validation:** Integrated **Elastio** with AWS Backup.
  - Automated Malware scanning on backups.
  - Performed hourly _Restore Testing_ to ensure backup viability (the "0 error" strategy).

---

### Conclusion

The event shifted my mindset from merely "backing up data" to "building resiliency." In an era where cyberattacks are inevitable, possessing a Data Resiliency strategy featuring **Immutability** and **Automation** (using tools like Elastio or Commvault) is vital for business survival.

#### Some photos from the event

- Add your photos here
  > Overall, the event not only provided technical knowledge but also helped me change my thinking regarding application design, system modernization, and more effective team collaboration.

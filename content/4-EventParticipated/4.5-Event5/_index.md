---
title: "Event 5"
date: "2025-11-29"
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# Summary Report: AWS Well-Architected Security Pillar

### Event Objectives 
- Manage secure access and permissions (IAM), including SSO, roles, and policies
- Implement Detection and Monitoring to detect threats early and automate alerts
- Protect infrastructure and workloads using network design, Security Groups, NACLs, WAF, and Shield
- Encrypt data and manage secrets (Data Protection)
- Standardize Incident Response processes and automate response actions

### Speakers

- **Tran Duc Anh** - Cloud Security Engineer Trainee
- **Nguyen Tuan Thinh** - Cloud Engineer Trainee
- **Nguyen Do Thanh Dat** - Cloud Engineer Trainee
- **Thinh Lam** - FCJer
- **Viet Nguyen** - FCJer
- **Mendel Grabski** - Head of Security and DevOps

### Key Highlights

#### Identity and Access Management (IAM) 

- IAM: Users, Roles, Policies – remove long-term credentials  
- IAM Identity Center: SSO, permission sets  
- Multi-account best practices:
  - Service Control Policies (SCP) for organization guardrails  
  - Permission boundaries to limit privilege escalation  
- Security enhancements:
  - Mandatory MFA  
  - Credential rotation  

#### Detection and Continuous Monitoring  

- AWS CloudTrail at organization level  
- Amazon GuardDuty for advanced threat detection  
- Logging:
  - VPC Flow Logs  
  - ALB Logs  
  - S3 Access Logs  
- Alerting & Automation:
  - EventBridge + SNS  
  - Lambda remediation  
- Apply Detection-as-Code using IaC  

#### Infrastructure Protection  

- Network design:
  - Public/Private subnets  
  - VPC segmentation  
- Security Groups (stateful) vs NACLs (stateless)  
- Network protection:
  - AWS WAF  
  - AWS Shield  
  - AWS Network Firewall  

#### Data Protection  

- AWS KMS 
- Encryption at-rest & in-transit 
- Secrets management:
  - Secrets Manager  
  - Parameter Store  
- Data classification and access guardrails  

## Incident Response  

- AWS Incident Response lifecycle  
- Common scenarios:
  - Compromised IAM key  
  - Public S3 exposure  
  - EC2 malware detection  
- Response workflow:
  - Snapshot resources  
  - Isolate workloads  
  - Collect evidence  
- Automation:
  - Lambda  
  - Step Functions  
  - Auto-remediation  

### Key Learnings

#### IAM is the foundation  
- Remove long-term credentials  
- Multi-account + SCP increases control  

#### Detection is essential  
- CloudTrail + GuardDuty allow early detection  
- Full logging supports forensic analysis  

#### Infrastructure requires defense in depth  
- Combine SGs, NACLs, WAF, Shield, Network Firewall  

#### Data must be protected and managed  
- Use KMS for encryption  
- Secrets Manager replaces hard-coded secrets  

#### Incident Response needs a clear process  
- Prepare playbooks in advance  

### Applying to Work

- Enable GuardDuty and Security Hub across all accounts  
- Configure EventBridge + Lambda for auto-remediation  
- Enable encryption by default for S3/EBS/RDS  

### Event Experience

Attending the “AWS Well-Architected Security Pillar” event provided practical and comprehensive insights into cloud security practices on AWS.

#### Highlights
- Deep understanding of Zero Trust & IAM  
- Guidance on enterprise-grade logging & alerting  
- Hands-on incident response demo  

#### Lessons Learned
- Cloud security = processes + people + tools  
- IAM and Detection are top priorities  
- Automating IR is key to reducing MTTR  
- Defense in depth is mandatory  

#### Event moments

![event 5](/images/4-Event/event5.jpg)

> The event was highly informative, providing actionable knowledge for cloud engineers, security specialists, and system administrators to improve AWS security operations

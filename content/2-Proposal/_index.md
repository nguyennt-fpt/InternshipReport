---
title: "Proposal"
date: "2024-01-01"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Secure Serverless Application Proposal

## 1. Project Summary
The project operates a serverless application on AWS with a fully automated CI/CD pipeline. When code is pushed to GitLab, the pipeline automatically scans for security vulnerabilities (Semgrep, Trivy), builds containers, provisions infrastructure via Terraform, and deploys images to Amazon ECR. Users access the application through Route 53 → CloudFront → WAF → API Gateway, authenticated by Cognito, invoking Lambda functions for business logic and storing data in DynamoDB. The system continuously monitors through CloudWatch, CloudTrail, and GuardDuty, sending alerts via EventBridge → SNS upon incidents or threats.

## 2. Problem Statement

### Current Problems
Many organizations struggle with deploying serverless applications due to error-prone manual processes, lack of security controls, and scalability challenges. Specific issues include:

- Manual deployment processes lack consistent security checks, often missing vulnerability scanning and automated testing steps
- Existing systems do not provide high-performance content delivery (CDN) nor application-layer protection against DDoS and bot attacks
- Secrets, encryption keys, and IAM permissions are difficult to manage end-to-end across the CI/CD pipeline while maintaining least-privilege principles
- Limited observability causes slow incident response and delayed threat detection

### Solution
The project delivers a complete serverless architecture with automated CI/CD, integrated security, and comprehensive monitoring. The deployment pipeline automates from GitLab through CodePipeline, CodeBuild, Semgrep (SAST), Trivy (container scanning), Terraform (IaC), and CodeDeploy, ensuring every change is tested and security-scanned. The delivery layer uses Route 53, CloudFront, and WAF to accelerate and protect the application. The serverless backend with Cognito (authentication), API Gateway, Lambda, DynamoDB, Secrets Manager, and KMS ensures data security. A multi-layer monitoring system (CloudWatch, CloudTrail, GuardDuty, EventBridge, SNS) detects and alerts on incidents instantly.

### Benefits and Return on Investment (ROI)
The solution delivers significant technical and financial benefits: full automation reduces deployment time by 80-90% and eliminates manual errors, integrated security reduces vulnerability risks, CDN and serverless architecture ensure high performance with automatic scaling, and centralized monitoring enables 70-80% faster incident detection. Financially, operational costs are estimated at ~$20-40/month for small to medium scale, significantly lower than traditional infrastructure, with no upfront hardware investment and 60-70% reduction in operational labor costs. Expected payback period is 1-2 months, while providing an extensible foundation for future projects.

## 3. System Architecture
![System Architecture](/images/2-Proposal/platform_architecture.jpeg)

The architecture is divided into three domains:

1. **CI/CD Pipeline**: Running on GitLab and AWS CodePipeline to build containers, scan security (Semgrep, Trivy), provision infrastructure via Terraform, and deploy images to Amazon ECR
2. **Content Delivery & Protection Layer**: Using Route 53, AWS WAF, and CloudFront to accelerate and secure user access
3. **Serverless Application Core**: Located in ap-southeast-1 region with Cognito (authentication), API Gateway, Lambda (business logic), DynamoDB (storage), KMS and Secrets Manager (security), and the observability suite (CloudWatch, CloudTrail, GuardDuty, EventBridge, SNS)

**AWS Services Used**
- GitLab Actions, AWS CodePipeline, CodeBuild, CodeDeploy
- Semgrep, Trivy
- Terraform, Amazon ECR
- Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon DynamoDB
- AWS WAF, Amazon CloudFront, Amazon Route 53
- AWS Secrets Manager, AWS Key Management Service (KMS)
- Amazon CloudWatch, AWS CloudTrail, Amazon GuardDuty, Amazon EventBridge, Amazon SNS

**Component Design**
- *CI/CD*: A Git push triggers the pipeline to run Semgrep (SAST), build containers in CodeBuild, scan with Trivy, execute Terraform Plan/Apply, and deliver via CodeDeploy.
- *Delivery & Protection*: Route 53 handles DNS → CloudFront caches content → WAF filters malicious traffic before reaching the APIs.
- *Application Services*: Cognito issues tokens, API Gateway validates requests and forwards them to Lambda, which processes business logic and reads/writes DynamoDB.
- *Secrets & Encryption*: Secrets Manager stores sensitive information, KMS encrypts data and keys.
- *Monitoring & Alerting*: CloudWatch aggregates metrics/logs, CloudTrail records audit trails, GuardDuty detects threats, EventBridge routes anomalies to SNS notifications.

## 4. Technical Implementation
1. Model the infrastructure with Terraform: logical VPC boundaries, least-privilege IAM roles, API Gateway, Lambda, DynamoDB, and security controls.
2. Configure GitLab CI/CD with AWS CodePipeline/CodeBuild/CodeDeploy using cross-account IAM roles.
3. Enforce Semgrep and Trivy scans in every pipeline run, failing builds on high-severity findings.
4. Define Terraform modules for Lambda (container images in ECR), API Gateway, CloudFront, WAF, and Cognito resources.
5. Enable CloudWatch log groups, metric filters, GuardDuty, CloudTrail; create EventBridge rules that fan out to SNS alerts.
6. Implement automated rollback via CodeDeploy deployment groups and versioned Terraform state.

## 5. Timeline & Milestones
| Phase | Schedule | Key Deliverables |
| --- | --- | --- |
| Kick-off | Week 1 | Requirements intake, detailed design, IAM role matrix |
| IaC & CI/CD Setup | Week 2 | Terraform baseline, GitLab pipeline, CodePipeline integration |
| Security Integration | Week 3 | Semgrep, Trivy, WAF rules, Cognito, Secrets Manager |
| Backend Completion | Week 4 | Lambda handlers, API Gateway routes, DynamoDB schema, unit tests |
| Deployment & Testing | Week 5 | End-to-end pipeline run, integration and CDN performance testing |
| Operations & Handover | Week 6 | Optimize monitoring systems, configure intelligent alerting, finalize documentation |
| Post-Deployment | Week 7+ | Continuous performance and cost monitoring, configuration optimization, feature expansion as needed, periodic security updates |

## 6. Budget Estimation
| AWS Service | Primary Billing Factor | Estimated Cost (USD) |
| --- | --- | --- |
| AWS Lambda & API Gateway | 250,000 requests/month | ~$1.00 - $3.00 |
| Amazon DynamoDB | 5 GB storage, 5 RCU/5 WCU (Provisioned) | ~$5.00 - $10.00 |
| CI/CD (CodePipeline/CodeBuild) | 5 deployments (250 build minutes) | ~$2.25 - $4.00 |
| Amazon ECR | 5 GB image storage ($0.10/GB) | ~$0.50 - $1.50 |
| Route 53 | 1 Hosted Zone + queries | ~$0.54 |
| CloudFront / WAF | 15 GB Data Transfer Out, basic WAF usage | ~$9.50 - $15.50 |
| Security & Monitoring | GuardDuty, KMS, Secrets Manager, minimal logs | ~$2.00 - $5.00 |
| **Total Cost** | | **~$20.79 - $39.54 USD/month** |

*Note: Actual spend varies with traffic and configuration; costs can drop further by shutting down dev/test environments. No hardware expenditure required.*

## 7. Risk Assessment
| Risk | Impact | Likelihood | Mitigation |
| --- | --- | --- | --- |
| Undetected vulnerabilities | High | Medium | Mandatory Semgrep/Trivy scans, enable AWS Inspector, periodic manual reviews |
| Misconfigured Terraform causing downtime | High | Low | Peer review Terraform plans, use staging environments, back up state | 
| Unexpected CDN or Terraform cost spikes | Medium | Medium | Configure AWS Budgets, tune CloudFront cache policies, monitor spend |
| Cognito authentication issues | Medium | Low | Implement automated auth tests, maintain backup user pool in another Region |

### Mitigation Strategies
- **Security**: Integrate automated security scanning (Semgrep, Trivy) into every pipeline, enable AWS Inspector periodically, conduct code reviews and quarterly security audits
- **Infrastructure Configuration**: Apply Infrastructure as Code (Terraform) with version control, peer review for all changes, use staging environments for testing before production, regularly backup Terraform state
- **Cost Management**: Set up AWS Budgets with 80% and 100% alert thresholds, configure optimized CloudFront cache policies, monitor costs daily via Cost Explorer, automatically shut down dev/test resources when unused
- **Authentication & Access**: Deploy automated testing for API authentication, maintain backup Cognito user pool in another region, enable multi-factor authentication (MFA) for administrative accounts

### Contingency Plan
- **Security Incidents**: Upon detection of critical vulnerabilities, immediately rollback to previous version via CodeDeploy, isolate affected resources, notify security team and proceed with patching
- **Downtime from Misconfiguration**: Use Terraform state backup to restore previous configuration, temporarily route traffic to staging environment if needed, maintain runbook for quick rollback
- **Budget Overrun**: Automatically shut down non-essential services when alert threshold is reached, optimize CloudFront cache hit ratio, consider switching to Reserved Capacity for DynamoDB if long-term usage
- **Authentication Failures**: Switch to backup Cognito user pool in another region, utilize API Gateway caching to reduce load, maintain fallback authentication mechanism

## 8. Expected Outcomes
- A secure, fully automated CI/CD pipeline where every change is scanned and tested before reaching production.
- A low-latency serverless backend served through CDN and API Gateway with pervasive data encryption.
- Centralized observability that surfaces incidents instantly and supports high availability SLAs.
- Optimized access control and cost efficiency delivered through least-privilege IAM and serverless scaling.
- An extensible infrastructure foundation ready for future features or additional AWS Regions.



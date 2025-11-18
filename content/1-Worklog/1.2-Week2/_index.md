---
title: "Week 2 Worklog"
date: "2025-09-15"
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---



### Week 2 Objectives:

* Learn how to manage and connect EC2 instances using session manager.
* Practice VPC peering and apply ACL and security group rules for cross-VPC communication.
* Understand the difference between VPC peering and transit gateway.
* Explore hybrid DNS with route 53 resolver and AWS managed microsoft AD.
* Deploy applications on EC2.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Learn about session manager <br> - **Practice:** <br>&emsp; + Create VPC endpoint for session manager <br>&emsp; + Create S3 bucket to store session logs                                                                | 09/15/2025 | 09/15/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Learn about VPC peering <br> - How to apply rules in ACLs and security groups for cross-VPC traffic <br> - **Practice:** <br>&emsp; + Create 2 VPCs <br>&emsp; + Set up VPC peering <br>&emsp; + Apply rules in ACLs and security groups for cross-VPC traffic                                         | 09/16/2025 | 09/16/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Learn about transit gateway <br> - Understand the different between VPC peering and transit gateway <br> - **Practice:** <br>&emsp; + Create 4 VPCs <br>&emsp; + Create transit gateway and attachments <br> &emsp; + Configure transit gateway route table and VPC route tables | 09/17/2025 | 09/17/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Learn about hybrid DNS with route 53 resolver <br> - Learn about AWS directory service and AWS managed microsoft AD <br> - **Practice:** <br>&emsp; + Set up network infrastructure using AWS cloudFormation  <br>&emsp; + Configure inbound/outbound endpoints for DNS resolution                            | 09/18/2025 | 09/18/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Learn about application deployment on EC2 (Windows and Linux) <br> - **Practice:** <br>&emsp; + Deploying databases and web services via CLI on linux <br>&emsp; + Deploying databases and web services via GUI and CLI on windows                                                                                     | 09/18/2025 | 09/20/2025      | <https://cloudjourney.awsstudygroup.com/> |


### Week 2 Achievements:

* Successfully managed and connected to EC2 instances in both public and private subnets using AWS session manager.

* Established VPC peering between two VPCs and applied proper ACL and security group rules to enable cross-VPC communication.

* Validated and troubleshooted VPC peering connectivity using reachability analyzer to identify and fix route table and security group issues.

* Built multi-VPC connectivity with AWS transit gateway, including:
  * Transit gateway attachments
  * Configured transit gateway route table
  * Configured VPC route table
  * ...

* Implemented Hybrid DNS resolution using Route 53 Resolver.

* Set up AWS Managed Microsoft AD through AWS Directory Service and deployed network infrastructure using CloudFormation.
* Deployed applications on EC2 instances (Windows and Linux) using both CLI and GUI methods.
* ...


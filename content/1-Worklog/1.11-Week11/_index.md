---
title: "Week 11 Worklog"
date: "2025-11-17"
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:

* Practice containerized Node.js application deployment using Docker and Docker Compose
* Practice Infrastructure as Code (IaC) with Terraform to deploy AWS services
* Learn about prompt engineering

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Create a small Node.js project running in Docker <br>&emsp; + Create a Docker network <br>&emsp; + Run a database container <br>&emsp; + Run an app container and connect it to the database <br>&emsp; + Delete the app container and create it again <br>&emsp; + Check that data in the database persists | 11/17/2025 | 11/17/2025      | https://docs.docker.com/get-started/workshop/         |
| 3   | - Deploy Node.js app using Docker Compose <br>&emsp; + Write a docker-compose.yml file to define app and database services <br>&emsp; + Run docker-compose up and test deployment | 11/18/2025 | 11/18/2025      | https://docs.docker.com/get-started/workshop/                        |
| 4   | - Practice Infrastructure as Code (IaC) with Terraform <br>&emsp; + Write scripts to create AWS VPC, subnets, and security groups <br>&emsp; + Create NAT gateway, ALB, target groups <br>&emsp; + Create RDS instance and IAM policies <br>&emsp; + Create ECS cluster, task definitions, and service | 11/19/2025 | 11/21/2025      |                 |
| 5   | - Deploy AWS services using Terraform <br>&emsp; + Apply Terraform scripts to create AWS resources <br>&emsp; + Verify that resources are created correctly <br>&emsp; + Check that ECS services and RDS are running stably | 11/21/2025 | 11/21/2025      |                           |
| 6   | - Learn prompt engineering techniques | 11/22/2025 | 11/22/2025      | https://youtu.be/_ZvnD73m40o?si=ud6xi6-S2NsvA7dr        |

### Week 11 Achievements:

* Successfully containerized a Node.js application using Docker  
    * Created a Docker network, database container, and app container  
    * Verified data persistence after recreating the app container  
    * Tested inter-container connectivity  

* Deployed Node.js app with Docker Compose  
    * Wrote a complete docker-compose.yml file including app and database services  
    * Ran docker-compose up and verified application functionality  

* Practiced Infrastructure as Code (IaC) using Terraform  
    * Created VPC, subnets, security groups, NAT gateway, ALB, and target groups  
    * Deployed RDS instance and configured IAM policies  
    * Created ECS cluster, task definitions, and services  

* Successfully deployed AWS infrastructure using Terraform  
    * Verified that all AWS resources were created correctly  
    * Confirmed ECS services and RDS instance were running stably  

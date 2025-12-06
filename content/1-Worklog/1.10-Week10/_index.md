---
title: "Week 10 Worklog"
date: "2025-11-10"
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---



### Week 10 Objectives:

* Reinforce AWS DevOps workflow using CodeBuild, CodePipeline, ECR, and ECS
* Practice building and deploying APIs using API Gateway and Lambda

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Use CodeBuild to automatically push image to ECR <br>&emsp; + Create a CodeBuild project and connect it to GitHub <br>&emsp; + Create the required IAM roles for CodeBuild <br>&emsp; + Write a buildspec.yml file to build and push a Docker image to Amazon ECR <br>&emsp; + Run a test build to verify that the image is successfully pushed to ECR                                                                                                 | 11/10/2025 | 11/10/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 3   | - Build a CI/CD Pipeline Using AWS CodePipeline <br> - **Practice:** <br>&emsp; + Create a CodePipeline pipeline that listens to GitHub pushes <br>&emsp; + Create required IAM roles for CodePipeline and deployment actions <br>&emsp; + Deploy container updates to ECS automatically <br>&emsp; + Push code to GitHub and verify that the ECS service updates successfully                                             | 11/11/2025 | 11/11/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 4   | - Learn about Amazon API Gateway <br>&emsp; + Create API resources <br>&emsp; + Create and integrate Lambda functions with API Gateway <br>&emsp; + Create methods and test invoking the Lambda function | 11/12/2025 | 11/12/2025      | https://youtube.com/playlist?list=PL4NoNM0L1m71akbZ8uzEOupMULmMpR-s7&si=kSCnIDdngdWk4C6D |
| 5   | - Practice Input Validation in API Gateway <br> - **Practice:** <br>&emsp; + Create API Gateway models to validate request headers and body <br>&emsp; + Add mapping templates for the method request <br>&emsp; + Test validation logic using the API Gateway console                      | 11/13/2025 | 11/13/2025      | https://youtube.com/playlist?list=PL4NoNM0L1m71akbZ8uzEOupMULmMpR-s7&si=kSCnIDdngdWk4C6D |
| 6   | - Use stage variables for multi-environment deployment <br> - **Practice:** <br>&emsp; + Create a Lambda function and versions <br>&emsp; + Create aliases and map them to different stages <br>&emsp; + Add stage variables and reference them in Lambda integration <br>&emsp; + Test and verify successful deployment                                                                                   | 11/14/2025 | 11/14/2025      | https://youtube.com/playlist?list=PL4NoNM0L1m71akbZ8uzEOupMULmMpR-s7&si=kSCnIDdngdWk4C6D |


### Week 10 Achievements:

* Strengthened AWS DevOps practices with CodeBuild, CodePipeline, ECR, and ECS
    * Created a CodeBuild project
    * Wrote and tested a complete buildspec.yml to build and push images to Amazon ECR
    * Set up a CodePipeline pipeline triggered on GitHub pushes
    * Integrated source, build, deploy stages to automate container updates on ECS
    * Tested end-to-end deployment and confirmed ECS service updates after each push

* Got familiar with API Gateway architecture
    * Created resources, methods, and Lambda integrations
    * Built a simple backend API using API Gateway and Lambda
    * Tested method responses and Lambda invocation

* Implemented request validation in API Gateway
    * Created request models to validate headers and request body
    * Added and configured mapping templates for method requests
    * Verified validation logic using the API Gateway console

* Practiced multi-environment deployment using stage variables
    * Created Lambda versions and aliases for different environments
    * Configured stage variables to dynamically select Lambda versions
    * Tested multi-stage behavior for dev, staging, and prod
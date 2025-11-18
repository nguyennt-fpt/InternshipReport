---
title: "Week 9 Worklog"
date: "2025-11-03"
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---



### Week 9 Objectives:

* Deploy containers on ECS using EC2 and Fargate
* Understand ECS networking modes
* Implement load balancing and path-based routing with ALB

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Practice running containers on ECS with EC2: <br>&emsp; + Create an ECR repository <br>&emsp; + Build and upload the container image to ECR <br>&emsp; + Create an ECS cluster <br>&emsp; + Define a task definition using EC2 <br>&emsp; + Create a service to launch the task <br>&emsp; + Verify the deployment is successful                                                                                               | 11/03/2025 | 11/03/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 3   | - Practice running containers on ECS with Fargate: <br>&emsp; + Push the container image to Docker Hub <br>&emsp; + Create a task definition for Fargate <br>&emsp; + Create a service and launch the task <br>&emsp; + Scale the service by increasing the desired task count <br>&emsp; + Verify the deployment is successful                                                                                               | 11/04/2025 | 11/04/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 4   | - Learn about ECS network modes <br> - Understand and compare the following network modes: <br>&emsp; + bridge <br>&emsp; + host <br>&emsp; + awsvpc  | 11/05/2025 | 11/05/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 5   | - Deploy containers with Application Load Balancer: <br>&emsp; + Create an Application Load Balancer <br>&emsp; + Create a target group <br>&emsp; + Create a service integrated with the ALB <br>&emsp; + Test and verify that traffic is routed correctly                      | 11/06/2025 | 11/06/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 6   | - Run multiple services behind the same ALB: <br>&emsp; + Create separate target groups for different paths (/email, /user) <br>&emsp; + Create services mapped to each target group <br>&emsp; + Test the deployment to ensure correct path routing                                                                                   | 11/07/2025 | 11/07/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |


### Week 9 Achievements:

* Practiced running containerized applications on Amazon ECS:
    * Built and pushed container images to both ECR and Docker Hub
    * Created ECS clusters using both EC2 and Fargate launch types
    * Created task definitions and deployed services
    * Scaled services by adjusting the desired task count
    * Verified successful application deployment and accessibility

* Learned about ECS network modes:
    * Compared the characteristics and use cases of bridge, host, and awsvpc modes
    * Understood how awsvpc mode assigns ENIs and improves service isolation

* Gained hands-on experience deploying containers with Application Load Balancer (ALB)
    * Created an ALB and configured target groups
    * Integrated ECS services with the ALB
    * Tested routing and confirmed traffic forwarding worked correctly

* Deployed multiple services behind a single ALB
    * Created multiple target groups for different application paths (/email, /user, /)
    * Mapped ECS services to the corresponding target groups
    * Tested and verified correct path-based routing behavior


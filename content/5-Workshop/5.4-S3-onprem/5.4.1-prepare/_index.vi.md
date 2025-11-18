---
title : "Chuáº©n bá»‹ tÃ i nguyÃªn"
date: "2024-01-01"
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

Äá»ƒ chuáº©n bá»‹ cho pháº§n nÃ y cá»§a workshop, báº¡n sáº½ cáº§n pháº£i:
+ Triá»ƒn khai CloudFormation stack
+ Sá»­a Ä‘á»•i báº£ng Ä‘á»‹nh tuyáº¿n VPC.

CÃ¡c thÃ nh pháº§n nÃ y hoáº¡t Ä‘á»™ng cÃ¹ng nhau Ä‘á»ƒ mÃ´ phá»ng DNS forwarding vÃ  name resolution.

#### Triá»ƒn khai CloudFormation stack

Máº«u CloudFormation sáº½ táº¡o cÃ¡c dá»‹ch vá»¥ bá»• sung Ä‘á»ƒ há»— trá»£ mÃ´ phá»ng mÃ´i trÆ°á»ng truyá»n thá»‘ng:
+ Má»™t Route 53 Private Hosted Zone lÆ°u trá»¯ cÃ¡c báº£n ghi BÃ­ danh (Alias records) cho Ä‘iá»ƒm cuá»‘i PrivateLink S3
+ Má»™t Route 53 Inbound Resolver endpoint cho phÃ©p "VPC Cloud" giáº£i quyáº¿t cÃ¡c yÃªu cáº§u resolve DNS gá»­i Ä‘áº¿n Private Hosted Zone
+ Má»™t Route 53 Outbound Resolver endpoint cho phÃ©p "VPC On-prem" chuyá»ƒn tiáº¿p cÃ¡c yÃªu cáº§u DNS cho S3 sang "VPC Cloud"

![route 53 diagram](/images/5-Workshop/5.4-S3-onprem/route53.png)

1. Click link sau Ä‘á»ƒ má»Ÿ [AWS CloudFormation console](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://s3.amazonaws.com/reinvent-endpoints-builders-session/R53CF.yaml&stackName=PLOnpremSetup). Máº«u yÃªu cáº§u sáº½ Ä‘Æ°á»£c táº£i sáºµn vÃ o menu. Cháº¥p nháº­n táº¥t cáº£ máº·c Ä‘á»‹nh vÃ  nháº¥p vÃ o Táº¡o stack.

![Create stack](/images/5-Workshop/5.4-S3-onprem/create-stack.png)

![Button](/images/5-Workshop/5.4-S3-onprem/create-stack-button.png)

CÃ³ thá»ƒ máº¥t vÃ i phÃºt Ä‘á»ƒ triá»ƒn khai stack hoÃ n táº¥t. Báº¡n cÃ³ thá»ƒ tiáº¿p tá»¥c vá»›i bÆ°á»›c tiáº¿p theo mÃ  khÃ´ng cáº§n Ä‘á»£i quÃ¡ trÃ¬nh triá»ƒn khai káº¿t thÃºc.

####  Cáº­p nháº­t báº£ng Ä‘á»‹nh tuyáº¿n private on-premise 

Workshop nÃ y sá»­ dá»¥ng StrongSwan VPN cháº¡y trÃªn EC2 instance Ä‘á»ƒ mÃ´ phá»ng kháº£ nÄƒng káº¿t ná»‘i giá»¯a trung tÃ¢m dá»¯ liá»‡u truyá»n thá»‘ng vÃ  mÃ´i trÆ°á»ng cloud AWS. Háº§u háº¿t cÃ¡c thÃ nh pháº§n báº¯t buá»™c Ä‘á»u Ä‘Æ°á»£c cung cáº¥p trÆ°á»›c khi báº¡n báº¯t Ä‘áº§u. Äá»ƒ hoÃ n táº¥t cáº¥u hÃ¬nh VPN, báº¡n sáº½ sá»­a Ä‘á»•i báº£ng Ä‘á»‹nh tuyáº¿n "VPC on-prem" Ä‘á»ƒ hÆ°á»›ng lÆ°u lÆ°á»£ng Ä‘áº¿n cloud Ä‘i qua StrongSwan VPN instance.

1. Má»Ÿ Amazon EC2 console 

2. Chá»n instance tÃªn infra-vpngw-test. Tá»« Details tab, copy Instance ID vÃ  paste vÃ o text editor cá»§a báº¡n Ä‘á»ƒ sá»­ dá»¥ng á»Ÿ nhá»¯ng bÆ°á»›c tiáº¿p theo

![ec2 id](/images/5-Workshop/5.4-S3-onprem/ec2-onprem-id.png)

3. Äi Ä‘áº¿n VPC menu báº±ng cÃ¡ch gÃµ "VPC" vÃ o Search box

4. Click vÃ o Route Tables, chá»n RT Private On-prem route table, chá»n Routes tab, vÃ  click Edit Routes.

![rt](/images/5-Workshop/5.4-S3-onprem/rt.png)

5. Click Add route.
+ Destination: CIDR block cá»§a Cloud VPC
+ Target: ID cá»§a infra-vpngw-test instance (báº¡n Ä‘Ã£ lÆ°u láº¡i á»Ÿ bÆ°á»›c trÃªn)

![add route](/images/5-Workshop/5.4-S3-onprem/add-route.png)

6. Click Save changes






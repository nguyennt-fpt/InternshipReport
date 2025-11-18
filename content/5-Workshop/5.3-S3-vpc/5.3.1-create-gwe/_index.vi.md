---
title : "Táº¡o má»™t Gateway Endpoint"
date: "2024-01-01" 
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

1. Má»Ÿ [Amazon VPC console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Home:)
2. Trong thanh Ä‘iá»u hÆ°á»›ng, chá»n **Endpoints**, click **Create Endpoint**:

{{% notice note %}}
Báº¡n sáº½ tháº¥y 6 Ä‘iá»ƒm cuá»‘i VPC hiá»‡n cÃ³ há»— trá»£ AWS Systems Manager (SSM). CÃ¡c Ä‘iá»ƒm cuá»‘i nÃ y Ä‘Æ°á»£c Máº«u CloudFormation triá»ƒn khai tá»± Ä‘á»™ng cho workshop nÃ y.
{{% /notice %}}

![endpoint](/images/5-Workshop/5.3-S3-vpc/endpoints.png)

3. Trong Create endpoint console:
+ Äáº·t tÃªn cho endpoint: s3-gwe
+ Trong service category, chá»n **aws services**

![endpoint](/images/5-Workshop/5.3-S3-vpc/create-s3-gwe1.png)

+ Trong **Services**, gÃµ "s3" trong há»™p tÃ¬m kiáº¿m vÃ  chá»n dá»‹ch vá»¥ vá»›i loáº¡i **gateway**

![endpoint](/images/5-Workshop/5.3-S3-vpc/services.png)

+ Äá»‘i vá»›i VPC, chá»n **VPC Cloud** tá»« drop-down menu.
+ Äá»‘i vá»›i Route tables, chá»n báº£ng Ä‘á»‹nh tuyáº¿n mÃ  Ä‘Ã£ liÃªn káº¿t vá»›i 2 subnets (lÆ°u Ã½: Ä‘Ã¢y khÃ´ng pháº£i lÃ  báº£ng Ä‘á»‹nh tuyáº¿n chÃ­nh cho VPC mÃ  lÃ  báº£ng Ä‘á»‹nh tuyáº¿n thá»© hai do CloudFormation táº¡o).

![endpoint](/images/5-Workshop/5.3-S3-vpc/vpc.png)

+ Äá»‘i vá»›i Policy, Ä‘á»ƒ tÃ¹y chá»n máº·c Ä‘á»‹nh lÃ  Full access Ä‘á»ƒ cho phÃ©p toÃ n quyá»n truy cáº­p vÃ o dá»‹ch vá»¥. Báº¡n sáº½ triá»ƒn khai VPC endpoint policy trong pháº§n sau Ä‘á»ƒ chá»©ng minh viá»‡c háº¡n cháº¿ quyá»n truy cáº­p vÃ o S3 bucket dá»±a trÃªn cÃ¡c policies.

![endpoint](/images/5-Workshop/5.3-S3-vpc/policy.png)

+ KhÃ´ng thÃªm tag vÃ o VPC endpoint.
+ Click Create endpoint, click x sau khi nháº­n Ä‘Æ°á»£c thÃ´ng bÃ¡o táº¡o thÃ nh cÃ´ng.

![endpoint](/images/5-Workshop/5.3-S3-vpc/complete.png)


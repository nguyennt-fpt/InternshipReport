---
title : "Dá»n dáº¹p tÃ i nguyÃªn"
date: "2024-01-01"
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Dá»n dáº¹p tÃ i nguyÃªn

Xin chÃºc má»«ng báº¡n Ä‘Ã£ hoÃ n thÃ nh xong lab nÃ y!
Trong lab nÃ y, báº¡n Ä‘Ã£ há»c vá» cÃ¡c mÃ´ hÃ¬nh kiáº¿n trÃºc Ä‘á»ƒ truy cáº­p Amazon S3 mÃ  khÃ´ng sá»­ dá»¥ng Public Internet.

+ Báº±ng cÃ¡ch táº¡o Gateway endpoint, báº¡n Ä‘Ã£ cho phÃ©p giao tiáº¿p trá»±c tiáº¿p giá»¯a cÃ¡c tÃ i nguyÃªn EC2 vÃ  Amazon S3, mÃ  khÃ´ng Ä‘i qua Internet Gateway.
Báº±ng cÃ¡ch táº¡o Interface endpoint, báº¡n Ä‘Ã£ má»Ÿ rá»™ng káº¿t ná»‘i S3 Ä‘áº¿n cÃ¡c tÃ i nguyÃªn cháº¡y trÃªn trung tÃ¢m dá»¯ liá»‡u trÃªn chá»— cá»§a báº¡n thÃ´ng qua AWS Site-to-Site VPN hoáº·c Direct Connect.

#### Dá»n dáº¹p
1. Äiá»u hÆ°á»›ng Ä‘áº¿n Hosted Zones trÃªn phÃ­a trÃ¡i cá»§a báº£ng Ä‘iá»u khiá»ƒn Route 53. Nháº¥p vÃ o tÃªn cá»§a  s3.us-east-1.amazonaws.com zone. Nháº¥p vÃ o Delete vÃ  xÃ¡c nháº­n viá»‡c xÃ³a báº±ng cÃ¡ch nháº­p tá»« khÃ³a "delete".

![hosted zone](/images/5-Workshop/5.6-Cleanup/delete-zone.png)

2. Disassociate Route 53 Resolver Rule - myS3Rule from "VPC Onprem" and Delete it. 

![hosted zone](/images/5-Workshop/5.6-Cleanup/vpc.png)

4.Má»Ÿ console cá»§a CloudFormation vÃ  xÃ³a hai stack CloudFormation mÃ  báº¡n Ä‘Ã£ táº¡o cho bÃ i thá»±c hÃ nh nÃ y:
+ PLOnpremSetup
+ PLCloudSetup

![delete stack](/images/5-Workshop/5.6-Cleanup/delete-stack.png)

5. XÃ³a cÃ¡c S3 bucket

+ Má»Ÿ báº£ng Ä‘iá»u khiá»ƒn S3
+ Chá»n bucket chÃºng ta Ä‘Ã£ táº¡o cho lab, nháº¥p chuá»™t vÃ  xÃ¡c nháº­n lÃ  empty. Nháº¥p Delete vÃ  xÃ¡c nháº­n delete.
+ 
![delete s3](/images/5-Workshop/5.6-Cleanup/delete-s3.png)


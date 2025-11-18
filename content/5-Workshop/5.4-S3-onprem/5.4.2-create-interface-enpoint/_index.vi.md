---
title : "Táº¡o má»™t S3 Interface endpoint"
date: "2024-01-01"
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

Trong pháº§n nÃ y, báº¡n sáº½ táº¡o vÃ  kiá»ƒm tra Interface Endpoint  S3 báº±ng cÃ¡ch sá»­ dá»¥ng mÃ´i trÆ°á»ng truyá»n thá»‘ng mÃ´ phá»ng.

1. Quay láº¡i Amazon VPC menu. Trong thanh Ä‘iá»u hÆ°á»›ng bÃªn trÃ¡i, chá»n Endpoints, sau Ä‘Ã³ click Create Endpoint.

2. Trong Create endpoint console:
+ Äáº·t tÃªn interface endpoint
+ Trong Service category, chá»n **aws services** 

![name](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint1.png)

3.  Trong Search box, gÃµ S3 vÃ  nháº¥n Enter. Chá»n endpoint cÃ³ tÃªn com.amazonaws.us-east-1.s3. Äáº£m báº£o ráº±ng cá»™t Type cÃ³ giÃ¡ trá»‹ Interface.

![service](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint2.png)

4. Äá»‘i vá»›i VPC, chá»n VPC Cloud tá»« drop-down.
{{% notice warning %}}
Äáº£m báº£o ráº±ng báº¡n chá»n "VPC Cloud" vÃ  khÃ´ng pháº£i "VPC On-prem"
{{% /notice %}}
+ Má»Ÿ rá»™ng **Additional settings** vÃ  Ä‘áº£m báº£o ráº±ng Enable DNS name *khÃ´ng* Ä‘Æ°á»£c chá»n (sáº½ sá»­ dá»¥ng Ä‘iá»u nÃ y trong pháº§n tiáº¿p theo cá»§a workshop)

![vpc](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint3.png)

5. Chá»n 2 subnets trong AZs sau: us-east-1a and us-east-1b

![subnets](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint4.png)

6. Äá»‘i vá»›i Security group, chá»n SGforS3Endpoint:

![sg](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint5.png)

7. Giá»¯ default policy - full access vÃ  click Create endpoint

![success](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint-success.png)

ChÃºc má»«ng báº¡n Ä‘Ã£ táº¡o thÃ nh cÃ´ng S3 interface endpoint. á»ž bÆ°á»›c tiáº¿p theo, chÃºng ta sáº½ kiá»ƒm tra interface endpoint.


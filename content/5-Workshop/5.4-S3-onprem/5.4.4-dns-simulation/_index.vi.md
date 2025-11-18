---
title : "MÃ´ phá»ng On-premises DNS "
date: "2024-01-01"
weight : 4
chapter : false
pre : " <b> 5.4.4 </b> "
---

 AWS PrivateLink endpoint cÃ³ má»™t Ä‘á»‹a chá»‰ IP cá»‘ Ä‘á»‹nh trong tá»«ng AZ nÆ¡i chÃºng Ä‘Æ°á»£c triá»ƒn khai, trong suá»‘t thá»i gian tá»“n táº¡i cá»§a endpoint (cho Ä‘áº¿n khi endpoint bá»‹ xÃ³a). CÃ¡c Ä‘á»‹a chá»‰ IP nÃ y Ä‘Æ°á»£c gáº¯n vÃ o Elastic network interface (ENI). AWS khuyáº¿n nghá»‹ sá»­ dá»¥ng DNS Ä‘á»ƒ resolve Ä‘á»‹a chá»‰ IP cho endpoint Ä‘á»ƒ cÃ¡c á»©ng dá»¥ng downstream sá»­ dá»¥ng Ä‘á»‹a chá»‰ IP má»›i nháº¥t khi ENIs Ä‘Æ°á»£c thÃªm vÃ o AZ má»›i hoáº·c bá»‹ xÃ³a theo thá»i gian.

Trong pháº§n nÃ y, báº¡n sáº½ táº¡o má»™t quy táº¯c chuyá»ƒn tiáº¿p (forwarding rule) Ä‘á»ƒ gá»­i cÃ¡c yÃªu cáº§u resolve DNS tá»« mÃ´i trÆ°á»ng truyá»n thá»‘ng (mÃ´ phá»ng) Ä‘áº¿n Private Hosted Zone trÃªn Route 53. Pháº§n nÃ y táº­n dá»¥ng cÆ¡ sá»Ÿ háº¡ táº§ng do CloudFormation triá»ƒn khai trong pháº§n Chuáº©n bá»‹ mÃ´i trÆ°á»ng.

#### Táº¡o DNS Alias Records cho Interface endpoint
1. Click link Ä‘á»ƒ Ä‘i Ä‘áº¿n [Route 53 management console](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=us-east-1#) (Hosted Zones section).  Máº«u CloudFormation mÃ  báº¡n triá»ƒn khai trong pháº§n Chuáº©n bá»‹ mÃ´i trÆ°á»ng Ä‘Ã£ táº¡o Private Hosted Zone nÃ y. Nháº¥p vÃ o tÃªn cá»§a Private Hosted Zone, s3.us-east-1.amazonaws.com:

![hosted zone](/images/5-Workshop/5.4-S3-onprem/hosted-zone.png)

2. Táº¡o 1 record má»›i trong Private Hosted Zone:

![Create record](/images/5-Workshop/5.4-S3-onprem/create-record1.png)

+ Giá»¯ nguyÃªn Record name vÃ  record type
+ Alias Button: click Ä‘á»ƒ enable
+ Route traffic to: Alias to VPC Endpoint
+ Region: US East (N. Virginia) [us-east-1]
+ Chá»n endpoint: Paste tÃªn (Regional VPC Endpoint DNS) báº¡n Ä‘Ã£ lÆ°u láº¡i á»Ÿ pháº§n 4.3

![record1](/images/5-Workshop/5.4-S3-onprem/record1.png)

3. Click Add another record, vÃ  add 1 cÃ¡i record thá»© 2 sá»­ dá»¥ng nhá»¯ng thÃ´ng sá»‘ sau:
+ Record name: *.
+ Record type: giá»¯ giÃ¡ trá»‹ default (type A)
+ Alias Button: Click Ä‘á»ƒ enable
+ Route traffic to: Alias to VPC Endpoint
+ Region: US East (N. Virginia) [us-east-1]
+ Chá»n endpoint: Paste tÃªn (Regional VPC Endpoint DNS) báº¡n Ä‘Ã£ lÆ°u láº¡i á»Ÿ pháº§n 4.3
+ Click **Create records** 

![record 2](/images/5-Workshop/5.4-S3-onprem/record2.png)

Record má»›i xuáº¥t hiá»‡n trÃªn giao diá»‡n Route 53.

![result](/images/5-Workshop/5.4-S3-onprem/result.png)

#### Táº¡o má»™t Resolver Forwarding Rule

**Route 53 Resolver Forwarding Rules** cho phÃ©p báº¡n chuyá»ƒn tiáº¿p cÃ¡c DNS queries tá»« VPC cá»§a báº¡n Ä‘áº¿n cÃ¡c nguá»“n khÃ¡c Ä‘á»ƒ resolve name. BÃªn ngoÃ i mÃ´i trÆ°á»ng workshop, báº¡n cÃ³ thá»ƒ sá»­ dá»¥ng tÃ­nh nÄƒng nÃ y Ä‘á»ƒ chuyá»ƒn tiáº¿p cÃ¡c DNS queries tá»« VPC cá»§a báº¡n Ä‘áº¿n cÃ¡c mÃ¡y chá»§ DNS cháº¡y trÃªn on-premises. Trong pháº§n nÃ y, báº¡n sáº½ mÃ´ phá»ng má»™t on-premises conditional forwarder báº±ng cÃ¡ch táº¡o má»™t forwarding rule Ä‘á»ƒ chuyá»ƒn tiáº¿p cÃ¡c DNS queries for Amazon S3 Ä‘áº¿n má»™t Private Hosted Zone cháº¡y trong "VPC Cloud" Ä‘á»ƒ resolve PrivateLink interface endpoint regional DNS name.

1. Tá»« giao diá»‡n  **Route 53**, chá»n **Inbound endpoints** trÃªn thanh bÃªn trÃ¡i

2. Trong giao diá»‡n **Inbound endpoint**, Chá»n ID cá»§a Inbound endpoint.

![Inbound endpoint](/images/5-Workshop/5.4-S3-onprem/route53-1.png)

3. Sao chÃ©p 2 Ä‘á»‹a chá»‰ IP trong danh sÃ¡ch vÃ o trÃ¬nh chá»‰nh sá»­a.

![Ip addresses](/images/5-Workshop/5.4-S3-onprem/route53-2.png)

4. Tá»« giao diá»‡n Route 53, chá»n  **Resolver** > **Rules** vÃ  chá»n **Create rule**

![Ip addresses](/images/5-Workshop/5.4-S3-onprem/route53-3.png)

5. Trong giao diá»‡n **Create rule**

+ Name: myS3Rule
+ Rule type: Forward
+ Domain name: s3.us-east-1.amazonaws.com

![create rule](/images/5-Workshop/5.4-S3-onprem/route53-4.png)

+ VPC: VPC On-prem
+ Outbound endpoint: VPCOnpremOutboundEndpoint

![create rule](/images/5-Workshop/5.4-S3-onprem/route53-5.png)

+ Target IP Addresses: Ä‘iá»n cáº£ hai IP báº¡n Ä‘Ã£ lÆ°u trá»¯ trÃªn trÃ¬nh soáº¡n tháº£o (inbound endpoint addresses) vÃ  sau Ä‘Ã³ chá»n **Submit**

![create rule](/images/5-Workshop/5.4-S3-onprem/route53-6.png)

Báº¡n Ä‘Ã£ táº¡o thÃ nh cÃ´ng resolver forwarding rule. 

![create rule](/images/5-Workshop/5.4-S3-onprem/route53-7.png)

#### Kiá»ƒm tra on-premises DNS mÃ´ phá»ng.

1. Káº¿t ná»‘i Ä‘áº¿n **Test-Interface-Endpoint EC2 instance** vá»›i **Session Manager**

![create rule](/images/5-Workshop/5.4-S3-onprem/test1.png)

2. Kiá»ƒm tra DNS resolution. Lá»‡nh dig sáº½ tráº£ vá» Ä‘á»‹a chá»‰ IP Ä‘Æ°á»£c gÃ¡n cho VPC endpoint interface Ä‘ang cháº¡y trÃªn VPC (Ä‘á»‹a chá»‰ IP cá»§a báº¡n sáº½ khÃ¡c):

```
dig +short s3.us-east-1.amazonaws.com 
```
{{% notice note %}}
CÃ¡c Ä‘á»‹a chá»‰ IP Ä‘Æ°á»£c tráº£ vá» lÃ  cÃ¡c Ä‘á»‹a chá»‰ IP VPC enpoint, KHÃ”NG pháº£i lÃ  cÃ¡c Ä‘á»‹a chá»‰ IP Resolver mÃ  báº¡n Ä‘Ã£ dÃ¡n tá»« trÃ¬nh chá»‰nh sá»­a vÄƒn báº£n cá»§a mÃ¬nh. CÃ¡c Ä‘á»‹a chá»‰ IP cá»§a  Resolver endpoint  vÃ   VPC endpoin trÃ´ng giá»‘ng nhau vÃ¬ chÃºng Ä‘á»u tá»« khá»‘i CIDR VPC Cloud.
{{% /notice %}}

![create rule](/images/5-Workshop/5.4-S3-onprem/dig.png)

3. Truy cáº­p vÃ o menu VPC (pháº§n Endpoints), chá»n S3 interface endpoint. Nháº¥p vÃ o tab Subnets vÃ  xÃ¡c nháº­n ráº±ng cÃ¡c Ä‘á»‹a chá»‰ IP Ä‘Æ°á»£c tráº£ vá» bá»Ÿi lá»‡nh Dig khá»›p vá»›i VPC endpoint:

![create rule](/images/5-Workshop/5.4-S3-onprem/subnet.png)

4. HÃ£y quay láº¡i shell cá»§a báº¡n vÃ  sá»­ dá»¥ng AWS CLI Ä‘á»ƒ kiá»ƒm tra danh sÃ¡ch cÃ¡c bucket S3 cá»§a báº¡n:

```
aws s3 ls --endpoint-url https://s3.us-east-1.amazonaws.com
```

![create rule](/images/5-Workshop/5.4-S3-onprem/endpoint.png)

5. Káº¿t thÃºc phiÃªn lÃ m viá»‡c cá»§a Session Manager cá»§a báº¡n:

![create rule](/images/5-Workshop/5.4-S3-onprem/terminal.png)


Trong pháº§n nÃ y, báº¡n Ä‘Ã£ táº¡o má»™t  **Interface Endpoint**  cho Amazon S3. Äiá»ƒm cuá»‘i nÃ y cÃ³ thá»ƒ Ä‘Æ°á»£c truy cáº­p tá»« on-premises thÃ´ng qua Site-to-Site VPN hoáº·c AWS Direct Connect. CÃ¡c Ä‘iá»ƒm cuá»‘i Route 53 Resolver outbound giáº£ láº­p chuyá»ƒn tiáº¿p cÃ¡c yÃªu cáº§u DNS tá»« on-premises Ä‘áº¿n má»™t Private Hosted Zone Ä‘ang cháº¡y trÃªn Ä‘Ã¡m mÃ¢y. CÃ¡c Ä‘iá»ƒm cuá»‘i Route 53 inbound nháº­n yÃªu cáº§u giáº£i quyáº¿t vÃ  tráº£ vá» má»™t pháº£n há»“i chá»©a Ä‘á»‹a chá»‰ IP cá»§a  **Interface Endpoint**  VPC. Sá»­ dá»¥ng DNS Ä‘á»ƒ giáº£i quyáº¿t cÃ¡c Ä‘á»‹a chá»‰ IP cá»§a Ä‘iá»ƒm cuá»‘i cung cáº¥p tÃ­nh sáºµn sÃ ng cao trong trÆ°á»ng há»£p má»™t Availability Zone gáº·p sá»± cá»‘.


---
title : "Kiá»ƒm tra Interface Endpoint"
date: "2024-01-01"
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

#### Láº¥y regional DNS name (tÃªn DNS khu vá»±c) cá»§a S3 interface endpoint
1. Trong Amazon VPC menu, chá»n Endpoints.

2. Click tÃªn cá»§a endpoint chÃºng ta má»›i táº¡o á»Ÿ má»¥c 4.2: s3-interface-endpoint. Click details vÃ  lÆ°u láº¡i regional DNS name cá»§a endpoint (cÃ¡i Ä‘áº§u tiÃªn) vÃ o text-editor cá»§a báº¡n Ä‘á»ƒ dÃ¹ng á»Ÿ cÃ¡c bÆ°á»›c sau.

![dns name](/images/5-Workshop/5.4-S3-onprem/dns.png)

#### Káº¿t ná»‘i Ä‘áº¿n EC2 instance á»Ÿ trong "VPC On-prem" (giáº£ láº­p mÃ´i trÆ°á»ng truyá»n thá»‘ng)

1. Äi Ä‘áº¿n **Session manager** báº±ng cÃ¡ch gÃµ "session manager" vÃ o Ã´ tÃ¬m kiáº¿m

2. Click **Start Session**, chá»n EC2 instance cÃ³ tÃªn **Test-Interface-Endpoint**. EC2 instance nÃ y Ä‘ang cháº¡y trÃªn "VPC On-prem" vÃ  sáº½ Ä‘Æ°á»£c sá»­ dá»¥ng Ä‘á»ƒ kiá»ƒm tra káº¿t ná»‘i Ä‘áº¿n Amazon S3 thÃ´ng qua Interface endpoint. Session Manager sáº½ má»Ÿ 1 browser tab má»›i vá»›i shell prompt: **sh-4.2 $**

![Start session](/images/5-Workshop/5.4-S3-onprem/start-session.png)

3. Äi Ä‘áº¿n ssm-user's home directory vá»›i lá»‡nh "cd ~"

4. Táº¡o 1 file tÃªn testfile2.xyz
```
fallocate -l 1G testfile2.xyz
```

![user](/images/5-Workshop/5.4-S3-onprem/cli1.png)

5. Copy file vÃ o S3 bucket mÃ¬nh táº¡o á»Ÿ section 4.2
```
aws s3 cp --endpoint-url https://bucket.<Regional-DNS-Name> testfile2.xyz s3://<your-bucket-name>
``` 
+ CÃ¢u lá»‡nh nÃ y yÃªu cáº§u thÃ´ng sá»‘ --endpoint-url, bá»Ÿi vÃ¬ báº¡n cáº§n sá»­ dá»¥ng DNS name chá»‰ Ä‘á»‹nh cho endpoint Ä‘á»ƒ truy cáº­p vÃ o S3 thÃ´ng qua Interface endpoint.
+ KhÃ´ng láº¥y ' * ' khi copy/paste tÃªn DNS khu vá»±c.
+ Cung cáº¥p tÃªn S3 bucket cá»§a báº¡n

![copy file](/images/5-Workshop/5.4-S3-onprem/cli2.png)

BÃ¢y giá» tá»‡p Ä‘Ã£ Ä‘Æ°á»£c thÃªm vÃ o bá»™ chá»©a S3 cá»§a báº¡n. HÃ£y kiá»ƒm tra bá»™ chá»©a S3 cá»§a báº¡n trong bÆ°á»›c tiáº¿p theo.

#### Kiá»ƒm tra Object trong S3 bucket

1. Äi Ä‘áº¿n S3 console
2. Click Buckets
3. Click tÃªn bucket cá»§a báº¡n vÃ  báº¡n sáº½ tháº¥y testfile2.xyz Ä‘Ã£ Ä‘Æ°á»£c thÃªm vÃ o s3 bucket cá»§a báº¡n

![check bucket](/images/5-Workshop/5.4-S3-onprem/check-bucket.png)






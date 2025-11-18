---
title : "VPC Endpoint Policies"
date: "2024-01-01"
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

Khi báº¡n táº¡o má»™t Interface Endpoint  hoáº·c cá»•ng, báº¡n cÃ³ thá»ƒ Ä‘Ã­nh kÃ¨m má»™t chÃ­nh sÃ¡ch Ä‘iá»ƒm cuá»‘i Ä‘á»ƒ kiá»ƒm soÃ¡t quyá»n truy cáº­p vÃ o dá»‹ch vá»¥ mÃ  báº¡n Ä‘ang káº¿t ná»‘i. ChÃ­nh sÃ¡ch VPC Endpoint lÃ  chÃ­nh sÃ¡ch tÃ i nguyÃªn IAM mÃ  báº¡n Ä‘Ã­nh kÃ¨m vÃ o Ä‘iá»ƒm cuá»‘i. Náº¿u báº¡n khÃ´ng Ä‘Ã­nh kÃ¨m chÃ­nh sÃ¡ch khi táº¡o Ä‘iá»ƒm cuá»‘i, thÃ¬ AWS sáº½ Ä‘Ã­nh kÃ¨m chÃ­nh sÃ¡ch máº·c Ä‘á»‹nh cho báº¡n Ä‘á»ƒ cho phÃ©p toÃ n quyá»n truy cáº­p vÃ o dá»‹ch vá»¥ thÃ´ng qua Ä‘iá»ƒm cuá»‘i.

Báº¡n cÃ³ thá»ƒ táº¡o chÃ­nh sÃ¡ch chá»‰ háº¡n cháº¿ quyá»n truy cáº­p vÃ o cÃ¡c S3 bucket cá»¥ thá»ƒ. Äiá»u nÃ y há»¯u Ã­ch náº¿u báº¡n chá»‰ muá»‘n má»™t sá»‘ Bá»™ chá»©a S3 nháº¥t Ä‘á»‹nh cÃ³ thá»ƒ truy cáº­p Ä‘Æ°á»£c thÃ´ng qua Ä‘iá»ƒm cuá»‘i.

Trong pháº§n nÃ y, báº¡n sáº½ táº¡o chÃ­nh sÃ¡ch VPC Endpoint háº¡n cháº¿ quyá»n truy cáº­p vÃ o S3 bucket Ä‘Æ°á»£c chá»‰ Ä‘á»‹nh trong chÃ­nh sÃ¡ch VPC Endpoint.

![endpoint diagram](/images/5-Workshop/5.5-Policy/s3-bucket-policy.png)

#### Káº¿t ná»‘i tá»›i EC2 vÃ  xÃ¡c minh káº¿t ná»‘i tá»›i S3. 

1. Báº¯t Ä‘áº§u má»™t phiÃªn AWS Session Manager má»›i trÃªn mÃ¡y chá»§ cÃ³ tÃªn lÃ  Test-Gateway-Endpoint. Tá»« phiÃªn nÃ y, xÃ¡c minh ráº±ng báº¡n cÃ³ thá»ƒ liá»‡t kÃª ná»™i dung cá»§a bucket mÃ  báº¡n Ä‘Ã£ táº¡o trong Pháº§n 1: Truy cáº­p S3 tá»« VPC.

```
aws s3 ls s3://<your-bucket-name>
```
![test](/images/5-Workshop/5.5-Policy/test1.png)

Ná»™i dung cá»§a bucket bao gá»“m hai tá»‡p cÃ³ dung lÆ°á»£ng 1GB Ä‘Ã£ Ä‘Æ°á»£c táº£i lÃªn trÆ°á»›c Ä‘Ã³.

2. Táº¡o má»™t bucket S3 má»›i; tuÃ¢n thá»§ máº«u Ä‘áº·t tÃªn mÃ  báº¡n Ä‘Ã£ sá»­ dá»¥ng trong Pháº§n 1, nhÆ°ng thÃªm '-2' vÃ o tÃªn. Äá»ƒ cÃ¡c trÆ°á»ng khÃ¡c lÃ  máº·c Ä‘á»‹nh vÃ  nháº¥p vÃ o **Create**.

![create bucket](/images/5-Workshop/5.5-Policy/create-bucket.png)

3. Táº¡o bucket thÃ nh cÃ´ng.

![Success](/images/5-Workshop/5.5-Policy/create-bucket-success.png)

Policy máº·c Ä‘á»‹nh cho phÃ©p truy cáº­p vÃ o táº¥t cáº£ cÃ¡c S3 Buckets thÃ´ng qua VPC endpoint.

4. Trong giao diá»‡n **Edit Policy**, sao chÃ©p vÃ  dÃ¡n theo policy sau, thay tháº¿ yourbucketname-2 vá»›i tÃªn bucket thá»© hai cá»§a báº¡n. Policy nÃ y sáº½ cho phÃ©p truy cáº­p Ä‘áº¿n bucket má»›i thÃ´ng qua VPC endpoint, nhÆ°ng khÃ´ng cho phÃ©p truy cáº­p Ä‘áº¿n cÃ¡c bucket cÃ²n láº¡i. Chá»n **Save** Ä‘á»ƒ kÃ­ch hoáº¡t policy.


```
{
  "Id": "Policy1631305502445",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1631305501021",
      "Action": "s3:*",
      "Effect": "Allow",
      "Resource": [
      				"arn:aws:s3:::yourbucketname-2",
       				"arn:aws:s3:::yourbucketname-2/*"
       ],
      "Principal": "*"
    }
  ]
}
```

![custom policy](/images/5-Workshop/5.5-Policy/policy2.png)

Cáº¥u hÃ¬nh policy thÃ nh cÃ´ng.

![success](/images/5-Workshop/5.5-Policy/success.png)

5. Tá»« session cá»§a báº¡n trÃªn Test-Gateway-Endpoint instance, kiá»ƒm tra truy cáº­p Ä‘áº¿n S3 bucket báº¡n táº¡o á»Ÿ bÆ°á»›c Ä‘áº§u

```
aws s3 ls s3://<yourbucketname>
```

CÃ¢u lá»‡nh tráº£ vá» lá»—i bá»Ÿi vÃ¬ truy cáº­p vÃ o S3 bucket khÃ´ng cÃ³ quyá»n trong VPC endpoint policy.

![error](/images/5-Workshop/5.5-Policy/error.png)

6. Trá»Ÿ láº¡i home directory cá»§a báº¡n trÃªn EC2 instance ```cd~```

+ Táº¡o file ```fallocate -l 1G test-bucket2.xyz ```
+ Sao chÃ©p file lÃªn bucket thá»©  2 ```aws s3 cp test-bucket2.xyz s3://<your-2nd-bucket-name>```

![success](/images/5-Workshop/5.5-Policy/test2.png)

Thao tÃ¡c nÃ y Ä‘Æ°á»£c cho phÃ©p bá»Ÿi VPC endpoint policy.

![success](/images/5-Workshop/5.5-Policy/test2-success.png)

Sau Ä‘Ã³ chÃºng ta kiá»ƒm tra truy cáº­p vÃ o S3 bucket Ä‘áº§u tiÃªn

 ```aws s3 cp test-bucket2.xyz s3://<your-1st-bucket-name>```

 ![fail](/images/5-Workshop/5.5-Policy/test2-fail.png)

 CÃ¢u lá»‡nh xáº£y ra lá»—i bá»Ÿi vÃ¬ bucket khÃ´ng cÃ³ quyá»n truy cáº­p bá»Ÿi VPC endpoint policy.

Trong pháº§n nÃ y, báº¡n Ä‘Ã£ táº¡o chÃ­nh sÃ¡ch VPC Endpoint cho Amazon S3 vÃ  sá»­ dá»¥ng AWS CLI Ä‘á»ƒ kiá»ƒm tra chÃ­nh sÃ¡ch. CÃ¡c hoáº¡t Ä‘á»™ng AWS CLI liÃªn quan Ä‘áº¿n bucket S3 ban Ä‘áº§u cá»§a báº¡n tháº¥t báº¡i vÃ¬ báº¡n Ã¡p dá»¥ng má»™t chÃ­nh sÃ¡ch chá»‰ cho phÃ©p truy cáº­p Ä‘áº¿n bucket thá»© hai mÃ  báº¡n Ä‘Ã£ táº¡o. CÃ¡c hoáº¡t Ä‘á»™ng AWS CLI nháº¯m vÃ o bucket thá»© hai cá»§a báº¡n thÃ nh cÃ´ng vÃ¬ chÃ­nh sÃ¡ch cho phÃ©p chÃºng. Nhá»¯ng chÃ­nh sÃ¡ch nÃ y cÃ³ thá»ƒ há»¯u Ã­ch trong cÃ¡c tÃ¬nh huá»‘ng khi báº¡n cáº§n kiá»ƒm soÃ¡t quyá»n truy cáº­p vÃ o tÃ i nguyÃªn thÃ´ng qua VPC Endpoint.


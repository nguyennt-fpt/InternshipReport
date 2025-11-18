---
title : "Kiá»ƒm tra Gateway Endpoint"
date: "2024-01-01" 
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

#### Táº¡o S3 bucket

1. Äi Ä‘áº¿n S3 management console
2. Trong Bucket console, chá»n **Create bucket**

![Create bucket](/images/5-Workshop/5.3-S3-vpc/create-bucket.png)

3. Trong Create bucket console
+ Äáº·t tÃªn bucket: chá»n 1 tÃªn mÃ  khÃ´ng bá»‹ trÃ¹ng trong pháº¡m vi toÃ n cáº§u (gá»£i Ã½: lab\<sá»‘-lab\>\<tÃªn-báº¡n\>)

![Bucket name](/images/5-Workshop/5.3-S3-vpc/bucket-name.png)


+ Giá»¯ nguyÃªn giÃ¡ trá»‹ cá»§a cÃ¡c fields khÃ¡c (default)
+ KÃ©o chuá»™t xuá»‘ng vÃ  chá»n **Create bucket**

![Create](/images/5-Workshop/5.3-S3-vpc/create-button.png)    

+ Táº¡o thÃ nh cÃ´ng S3 bucket

![Success](/images/5-Workshop/5.3-S3-vpc/bucket-success.png)

#### Káº¿t ná»‘i vá»›i EC2 báº±ng session manager

+ Trong workshop nÃ y, báº¡n sáº½ dÃ¹ng AWS Session Manager Ä‘á»ƒ káº¿t ná»‘i Ä‘áº¿n cÃ¡c EC2 instances. Session Manager lÃ  1 tÃ­nh nÄƒng trong dá»‹ch vá»¥ Systems Manager Ä‘Æ°á»£c quáº£n lÃ½ hoÃ n toÃ n bá»Ÿi AWS. System manager cho phÃ©p báº¡n quáº£n lÃ½ Amazon EC2 instances vÃ  cÃ¡c mÃ¡y áº£o on-premises (VMs)thÃ´ng qua 1 browser-based shell. Session Manager cung cáº¥p kháº£ nÄƒng quáº£n lÃ½ phiÃªn báº£n an toÃ n vÃ  cÃ³ thá»ƒ kiá»ƒm tra mÃ  khÃ´ng cáº§n má»Ÿ cá»•ng vÃ o, duy trÃ¬ mÃ¡y chá»§ bastion host hoáº·c quáº£n lÃ½ khÃ³a SSH.

+ First cloud journey [Lab](https://000058.awsstudygroup.com/1-introduce/) Ä‘á»ƒ hiá»ƒu sÃ¢u hÆ¡n vá» Session manager.

1. Trong AWS Management Console, gÃµ Systems Manager trong Ã´ tÃ¬m kiáº¿m vÃ  nháº¥n Enter:

![system manager](/images/5-Workshop/5.3-S3-vpc/sm.png)

2. Tá»« **Systems Manager** menu, tÃ¬m **Node Management** á»Ÿ thanh bÃªn trÃ¡i vÃ  chá»n **Session Manager**:

![system manager](/images/5-Workshop/5.3-S3-vpc/sm1.png)

3. Click Start Session, vÃ  chá»n EC2 instance tÃªn **Test-Gateway-Endpoint**. 
{{% notice info %}}
PhiÃªn báº£n EC2 nÃ y Ä‘Ã£ cháº¡y trong "VPC cloud" vÃ  sáº½ Ä‘Æ°á»£c dÃ¹ng Ä‘á»ƒ kiá»ƒm tra kháº£ nÄƒng káº¿t ná»‘i vá»›i Amazon S3 thÃ´ng qua Ä‘iá»ƒm cuá»‘i Cá»•ng mÃ  báº¡n vá»«a táº¡o (s3-gwe). {{% /notice %}}

![Start session](/images/5-Workshop/5.3-S3-vpc/start-session.png)

Session Manager sáº½ má»Ÿ browser tab má»›i vá»›i shell prompt: sh-4.2 $

![Success](/images/5-Workshop/5.3-S3-vpc/start-session-success.png)

Báº¡n Ä‘Ã£ báº¯t Ä‘áº§u phiÃªn káº¿t ná»‘i Ä‘áº¿n EC2 trong VPC Cloud thÃ nh cÃ´ng. Trong bÆ°á»›c tiáº¿p theo, chÃºng ta sáº½ táº¡o má»™t  S3 bucket vÃ  má»™t tá»‡p trong Ä‘Ã³.
#### Create a file and upload to s3 bucket

1. Äá»•i vá» ssm-user's thÆ° má»¥c báº±ng lá»‡nh "cd ~" 

![Change user's dir](/images/5-Workshop/5.3-S3-vpc/cli1.png)

2. Táº¡o 1 file Ä‘á»ƒ kiá»ƒm tra báº±ng lá»‡nh "fallocate -l 1G testfile.xyz", 1 file tÃªn "testfile.xyz" cÃ³ kÃ­ch thÆ°á»›c 1GB sáº½ Ä‘Æ°á»£c táº¡o.

![Create file](/images/5-Workshop/5.3-S3-vpc/cli-file.png)

3. Táº£i file mÃ¬nh vá»«a táº¡o lÃªn S3 vá»›i lá»‡nh "aws s3 cp testfile.xyz s3://your-bucket-name". Thay your-bucket-name báº±ng tÃªn S3 báº¡n Ä‘Ã£ táº¡o.

![Uploaded](/images/5-Workshop/5.3-S3-vpc/uploaded.png)

Báº¡n Ä‘Ã£ táº£i thÃ nh cÃ´ng tá»‡p lÃªn bá»™ chá»©a S3 cá»§a mÃ¬nh. BÃ¢y giá» báº¡n cÃ³ thá»ƒ káº¿t thÃºc session.

#### Kiá»ƒm tra object trong S3 bucket

1. Äi Ä‘áº¿n S3 console.  
2. Click tÃªn s3 bucket cá»§a báº¡n
3. Trong Bucket console, báº¡n sáº½ tháº¥y tá»‡p báº¡n Ä‘Ã£ táº£i lÃªn S3 bucket cá»§a mÃ¬nh

![Check S3](/images/5-Workshop/5.3-S3-vpc/check-s3-bucket.png)

#### TÃ³m táº¯t

ChÃºc má»«ng báº¡n Ä‘Ã£ hoÃ n thÃ nh truy cáº­p S3 tá»« VPC. Trong pháº§n nÃ y, báº¡n Ä‘Ã£ táº¡o gateway endpoint cho Amazon S3 vÃ  sá»­ dá»¥ng AWS CLI Ä‘á»ƒ táº£i file lÃªn. QuÃ¡ trÃ¬nh táº£i lÃªn hoáº¡t Ä‘á»™ng vÃ¬ gateway endpoint cho phÃ©p giao tiáº¿p vá»›i S3 mÃ  khÃ´ng cáº§n Internet gateway gáº¯n vÃ o "VPC Cloud". Äiá»u nÃ y thá»ƒ hiá»‡n chá»©c nÄƒng cá»§a gateway endpoint nhÆ° má»™t Ä‘Æ°á»ng dáº«n an toÃ n Ä‘áº¿n S3 mÃ  khÃ´ng cáº§n Ä‘i qua pub    lic Internet.


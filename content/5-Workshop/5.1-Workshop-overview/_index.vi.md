---
title : "Giá»›i thiá»‡u"
date: "2024-01-01" 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Giá»›i thiá»‡u vá» VPC Endpoint

+ Äiá»ƒm cuá»‘i VPC (endpoint) lÃ  thiáº¿t bá»‹ áº£o. ChÃºng lÃ  cÃ¡c thÃ nh pháº§n VPC cÃ³ thá»ƒ má»Ÿ rá»™ng theo chiá»u ngang, dá»± phÃ²ng vÃ  cÃ³ tÃ­nh sáºµn sÃ ng cao. ChÃºng cho phÃ©p giao tiáº¿p giá»¯a tÃ i nguyÃªn Ä‘iá»‡n toÃ¡n cá»§a báº¡n vÃ  dá»‹ch vá»¥ AWS mÃ  khÃ´ng gÃ¢y ra rá»§i ro vá» tÃ­nh sáºµn sÃ ng.
+ TÃ i nguyÃªn Ä‘iá»‡n toÃ¡n Ä‘ang cháº¡y trong VPC cÃ³ thá»ƒ truy cáº­p Amazon S3 báº±ng cÃ¡ch sá»­ dá»¥ng Ä‘iá»ƒm cuá»‘i Gateway. Interface Endpoint  PrivateLink cÃ³ thá»ƒ Ä‘Æ°á»£c sá»­ dá»¥ng bá»Ÿi tÃ i nguyÃªn cháº¡y trong VPC hoáº·c táº¡i TTDL.

#### Tá»•ng quan vá» workshop
Trong workshop nÃ y, báº¡n sáº½ sá»­ dá»¥ng hai VPC.
+ **"VPC Cloud"** dÃ nh cho cÃ¡c tÃ i nguyÃªn cloud nhÆ° Gateway endpoint vÃ  EC2 instance Ä‘á»ƒ kiá»ƒm tra.
+ **"VPC On-Prem"** mÃ´ phá»ng mÃ´i trÆ°á»ng truyá»n thá»‘ng nhÆ° nhÃ  mÃ¡y hoáº·c trung tÃ¢m dá»¯ liá»‡u cá»§a cÃ´ng ty. Má»™t EC2 Instance cháº¡y pháº§n má»m StrongSwan VPN Ä‘Ã£ Ä‘Æ°á»£c triá»ƒn khai trong "VPC On-prem" vÃ  Ä‘Æ°á»£c cáº¥u hÃ¬nh tá»± Ä‘á»™ng Ä‘á»ƒ thiáº¿t láº­p Ä‘Æ°á»ng háº§m VPN Site-to-Site vá»›i AWS Transit Gateway. VPN nÃ y mÃ´ phá»ng káº¿t ná»‘i tá»« má»™t vá»‹ trÃ­ táº¡i TTDL (on-prem) vá»›i AWS cloud. Äá»ƒ giáº£m thiá»ƒu chi phÃ­, chá»‰ má»™t phiÃªn báº£n VPN Ä‘Æ°á»£c cung cáº¥p Ä‘á»ƒ há»— trá»£ workshop nÃ y. Khi láº­p káº¿ hoáº¡ch káº¿t ná»‘i VPN cho production workloads cá»§a báº¡n, AWS khuyÃªn báº¡n nÃªn sá»­ dá»¥ng nhiá»u thiáº¿t bá»‹ VPN Ä‘á»ƒ cÃ³ tÃ­nh sáºµn sÃ ng cao.

![overview](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)


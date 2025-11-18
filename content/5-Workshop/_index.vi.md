---
title: "Workshop"
date: "2024-01-01"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

{{% notice warning %}}
âš ï¸ **LÆ°u Ã½:** CÃ¡c thÃ´ng tin dÆ°á»›i Ä‘Ã¢y chá»‰ nháº±m má»¥c Ä‘Ã­ch tham kháº£o, vui lÃ²ng **khÃ´ng sao chÃ©p nguyÃªn vÄƒn** cho bÃ i bÃ¡o cÃ¡o cá»§a báº¡n ká»ƒ cáº£ warning nÃ y.
{{% /notice %}}


# Äáº£m báº£o truy cáº­p Hybrid an toÃ n Ä‘áº¿n S3 báº±ng cÃ¡ch sá»­ dá»¥ng VPC endpoint

#### Tá»•ng quan

**AWS PrivateLink** cung cáº¥p káº¿t ná»‘i riÃªng tÆ° Ä‘áº¿n cÃ¡c dá»‹ch vá»¥ aws tá»« VPCs hoáº·c trung tÃ¢m dá»¯ liá»‡u (on-premise) mÃ  khÃ´ng lÃ m lá»™ lÆ°u lÆ°á»£ng truy cáº­p ra ngoÃ i public internet.

Trong bÃ i lab nÃ y, chÃºng ta sáº½ há»c cÃ¡ch táº¡o, cáº¥u hÃ¬nh, vÃ  kiá»ƒm tra VPC endpoints Ä‘á»ƒ cho phÃ©p workload cá»§a báº¡n tiáº¿p cáº­n cÃ¡c dá»‹ch vá»¥ AWS mÃ  khÃ´ng cáº§n Ä‘i qua Internet cÃ´ng cá»™ng.

ChÃºng ta sáº½ táº¡o hai loáº¡i endpoints Ä‘á»ƒ truy cáº­p Ä‘áº¿n Amazon S3: gateway vpc endpoint vÃ  interface vpc endpoint. Hai loáº¡i vpc endpoints nÃ y mang Ä‘áº¿n nhiá»u lá»£i Ã­ch tÃ¹y thuá»™c vÃ o viá»‡c báº¡n truy cáº­p Ä‘áº¿n S3 tá»« mÃ´i trÆ°á»ng cloud hay tá»« trung tÃ¢m dá»¯ liá»‡u (on-premise).
+ **Gateway** - Táº¡o gateway endpoint Ä‘á»ƒ gá»­i lÆ°u lÆ°á»£ng Ä‘áº¿n Amazon S3 hoáº·c DynamoDB using private IP addresses. Báº¡n Ä‘iá»u hÆ°á»›ng lÆ°u lÆ°á»£ng tá»« VPC cá»§a báº¡n Ä‘áº¿n gateway endpoint báº±ng cÃ¡c báº£ng Ä‘á»‹nh tuyáº¿n (route tables)
+ **Interface** - Táº¡o interface endpoint Ä‘á»ƒ gá»­i lÆ°u lÆ°á»£ng Ä‘áº¿n cÃ¡c dá»‹ch vá»¥ Ä‘iá»ƒm cuá»‘i (endpoints) sá»­ dá»¥ng Network Load Balancer Ä‘á»ƒ phÃ¢n phá»‘i lÆ°u lÆ°á»£ng. LÆ°u lÆ°á»£ng dÃ nh cho dá»‹ch vá»¥ Ä‘iá»ƒm cuá»‘i Ä‘Æ°á»£c resolved báº±ng DNS.

#### Ná»™i dung

1. [Tá»•ng quan vá» workshop](5.1-Workshop-overview/)
2. [Chuáº©n bá»‹](5.2-Prerequiste/)
3. [Truy cáº­p Ä‘áº¿n S3 tá»« VPC](5.3-S3-vpc/)
4. [Truy cáº­p Ä‘áº¿n S3 tá»« TTDL On-premises](5.4-S3-onprem/)
5. [VPC Endpoint Policies (lÃ m thÃªm)](5.5-Policy/)
6. [Dá»n dáº¹p tÃ i nguyÃªn](5.6-Cleanup/)


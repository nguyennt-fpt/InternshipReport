---
title: "Blog 6"
date: "2024-01-01"
weight: 1
chapter: false
pre: " <b> 3.6. </b> "
---

{{% notice warning %}}
âš ï¸ **LÆ°u Ã½:** CÃ¡c thÃ´ng tin dÆ°á»›i Ä‘Ã¢y chá»‰ nháº±m má»¥c Ä‘Ã­ch tham kháº£o, vui lÃ²ng **khÃ´ng sao chÃ©p nguyÃªn vÄƒn** cho bÃ i bÃ¡o cÃ¡o cá»§a báº¡n ká»ƒ cáº£ warning nÃ y.
{{% /notice %}}

# Báº¯t Ä‘áº§u vá»›i healthcare data lakes: Sá»­ dá»¥ng microservices

CÃ¡c data lake cÃ³ thá»ƒ giÃºp cÃ¡c bá»‡nh viá»‡n vÃ  cÆ¡ sá»Ÿ y táº¿ chuyá»ƒn dá»¯ liá»‡u thÃ nh nhá»¯ng thÃ´ng tin chi tiáº¿t vá» doanh nghiá»‡p vÃ  duy trÃ¬ hoáº¡t Ä‘á»™ng kinh doanh liÃªn tá»¥c, Ä‘á»“ng thá»i báº£o vá»‡ quyá»n riÃªng tÆ° cá»§a bá»‡nh nhÃ¢n. **Data lake** lÃ  má»™t kho lÆ°u trá»¯ táº­p trung, Ä‘Æ°á»£c quáº£n lÃ½ vÃ  báº£o máº­t Ä‘á»ƒ lÆ°u trá»¯ táº¥t cáº£ dá»¯ liá»‡u cá»§a báº¡n, cáº£ á»Ÿ dáº¡ng ban Ä‘áº§u vÃ  Ä‘Ã£ xá»­ lÃ½ Ä‘á»ƒ phÃ¢n tÃ­ch. data lake cho phÃ©p báº¡n chia nhá» cÃ¡c kho chá»©a dá»¯ liá»‡u vÃ  káº¿t há»£p cÃ¡c loáº¡i phÃ¢n tÃ­ch khÃ¡c nhau Ä‘á»ƒ cÃ³ Ä‘Æ°á»£c thÃ´ng tin chi tiáº¿t vÃ  Ä‘Æ°a ra cÃ¡c quyáº¿t Ä‘á»‹nh kinh doanh tá»‘t hÆ¡n.

BÃ i Ä‘Äƒng trÃªn blog nÃ y lÃ  má»™t pháº§n cá»§a loáº¡t bÃ i lá»›n hÆ¡n vá» viá»‡c báº¯t Ä‘áº§u cÃ i Ä‘áº·t data lake dÃ nh cho lÄ©nh vá»±c y táº¿. Trong bÃ i Ä‘Äƒng blog cuá»‘i cÃ¹ng cá»§a tÃ´i trong loáº¡t bÃ i, *â€œBáº¯t Ä‘áº§u vá»›i data lake dÃ nh cho lÄ©nh vá»±c y táº¿: ÄÃ o sÃ¢u vÃ o Amazon Cognitoâ€*, tÃ´i táº­p trung vÃ o cÃ¡c chi tiáº¿t cá»¥ thá»ƒ cá»§a viá»‡c sá»­ dá»¥ng Amazon Cognito vÃ  Attribute Based Access Control (ABAC) Ä‘á»ƒ xÃ¡c thá»±c vÃ  á»§y quyá»n ngÆ°á»i dÃ¹ng trong giáº£i phÃ¡p data lake y táº¿. Trong blog nÃ y, tÃ´i trÃ¬nh bÃ y chi tiáº¿t cÃ¡ch giáº£i phÃ¡p Ä‘Ã£ phÃ¡t triá»ƒn á»Ÿ cáº¥p Ä‘á»™ cÆ¡ báº£n, bao gá»“m cÃ¡c quyáº¿t Ä‘á»‹nh thiáº¿t káº¿ mÃ  tÃ´i Ä‘Ã£ Ä‘Æ°a ra vÃ  cÃ¡c tÃ­nh nÄƒng bá»• sung Ä‘Æ°á»£c sá»­ dá»¥ng. Báº¡n cÃ³ thá»ƒ truy cáº­p cÃ¡c code samples cho giáº£i phÃ¡p táº¡i Git repo nÃ y Ä‘á»ƒ tham kháº£o.

---

## HÆ°á»›ng dáº«n kiáº¿n trÃºc

Thay Ä‘á»•i chÃ­nh ká»ƒ tá»« láº§n trÃ¬nh bÃ y cuá»‘i cÃ¹ng cá»§a kiáº¿n trÃºc tá»•ng thá»ƒ lÃ  viá»‡c tÃ¡ch dá»‹ch vá»¥ Ä‘Æ¡n láº» thÃ nh má»™t táº­p há»£p cÃ¡c dá»‹ch vá»¥ nhá» Ä‘á»ƒ cáº£i thiá»‡n kháº£ nÄƒng báº£o trÃ¬ vÃ  tÃ­nh linh hoáº¡t. Viá»‡c tÃ­ch há»£p má»™t lÆ°á»£ng lá»›n dá»¯ liá»‡u y táº¿ khÃ¡c nhau thÆ°á»ng yÃªu cáº§u cÃ¡c trÃ¬nh káº¿t ná»‘i chuyÃªn biá»‡t cho tá»«ng Ä‘á»‹nh dáº¡ng; báº±ng cÃ¡ch giá»¯ chÃºng Ä‘Æ°á»£c Ä‘Ã³ng gÃ³i riÃªng biá»‡t vá»›i microservices, chÃºng ta cÃ³ thá»ƒ thÃªm, xÃ³a vÃ  sá»­a Ä‘á»•i tá»«ng trÃ¬nh káº¿t ná»‘i mÃ  khÃ´ng áº£nh hÆ°á»Ÿng Ä‘áº¿n nhá»¯ng káº¿t ná»‘i khÃ¡c. CÃ¡c microservices Ä‘Æ°á»£c káº¿t ná»‘i rá»i thÃ´ng qua tin nháº¯n publish/subscribe táº­p trung trong cÃ¡i mÃ  tÃ´i gá»i lÃ  â€œpub/sub hubâ€.

Giáº£i phÃ¡p nÃ y Ä‘áº¡i diá»‡n cho nhá»¯ng gÃ¬ tÃ´i sáº½ coi lÃ  má»™t láº§n láº·p nÆ°á»›c rÃºt há»£p lÃ½ khÃ¡c tá»« last post cá»§a tÃ´i. Pháº¡m vi váº«n Ä‘Æ°á»£c giá»›i háº¡n trong viá»‡c nháº­p vÃ  phÃ¢n tÃ­ch cÃº phÃ¡p Ä‘Æ¡n giáº£n cá»§a cÃ¡c **HL7v2 messages** Ä‘Æ°á»£c Ä‘á»‹nh dáº¡ng theo **Quy táº¯c mÃ£ hÃ³a 7 (ER7)** thÃ´ng qua giao diá»‡n REST.

**Kiáº¿n trÃºc giáº£i phÃ¡p bÃ¢y giá» nhÆ° sau:**

> *HÃ¬nh 1. Kiáº¿n trÃºc tá»•ng thá»ƒ; nhá»¯ng Ã´ mÃ u thá»ƒ hiá»‡n nhá»¯ng dá»‹ch vá»¥ riÃªng biá»‡t.*

---

Máº·c dÃ¹ thuáº­t ngá»¯ *microservices* cÃ³ má»™t sá»‘ sá»± mÆ¡ há»“ cá»‘ há»¯u, má»™t sá»‘ Ä‘áº·c Ä‘iá»ƒm lÃ  chung:  
- ChÃºng nhá», tá»± chá»§, káº¿t há»£p rá»i ráº¡c  
- CÃ³ thá»ƒ tÃ¡i sá»­ dá»¥ng, giao tiáº¿p thÃ´ng qua giao diá»‡n Ä‘Æ°á»£c xÃ¡c Ä‘á»‹nh rÃµ  
- ChuyÃªn biá»‡t Ä‘á»ƒ giáº£i quyáº¿t má»™t viá»‡c  
- ThÆ°á»ng Ä‘Æ°á»£c triá»ƒn khai trong **event-driven architecture**

Khi xÃ¡c Ä‘á»‹nh vá»‹ trÃ­ táº¡o ranh giá»›i giá»¯a cÃ¡c microservices, cáº§n cÃ¢n nháº¯c:  
- **Ná»™i táº¡i**: cÃ´ng nghá»‡ Ä‘Æ°á»£c sá»­ dá»¥ng, hiá»‡u suáº¥t, Ä‘á»™ tin cáº­y, kháº£ nÄƒng má»Ÿ rá»™ng  
- **BÃªn ngoÃ i**: chá»©c nÄƒng phá»¥ thuá»™c, táº§n suáº¥t thay Ä‘á»•i, kháº£ nÄƒng tÃ¡i sá»­ dá»¥ng  
- **Con ngÆ°á»i**: quyá»n sá»Ÿ há»¯u nhÃ³m, quáº£n lÃ½ *cognitive load*

---

## Lá»±a chá»n cÃ´ng nghá»‡ vÃ  pháº¡m vi giao tiáº¿p

| Pháº¡m vi giao tiáº¿p                        | CÃ¡c cÃ´ng nghá»‡ / mÃ´ hÃ¬nh cáº§n xem xÃ©t                                                        |
| ---------------------------------------- | ------------------------------------------------------------------------------------------ |
| Trong má»™t microservice                   | Amazon Simple Queue Service (Amazon SQS), AWS Step Functions                               |
| Giá»¯a cÃ¡c microservices trong má»™t dá»‹ch vá»¥ | AWS CloudFormation cross-stack references, Amazon Simple Notification Service (Amazon SNS) |
| Giá»¯a cÃ¡c dá»‹ch vá»¥                         | Amazon EventBridge, AWS Cloud Map, Amazon API Gateway                                      |

---

## The pub/sub hub

Viá»‡c sá»­ dá»¥ng kiáº¿n trÃºc **hub-and-spoke** (hay message broker) hoáº¡t Ä‘á»™ng tá»‘t vá»›i má»™t sá»‘ lÆ°á»£ng nhá» cÃ¡c microservices liÃªn quan cháº·t cháº½.  
- Má»—i microservice chá»‰ phá»¥ thuá»™c vÃ o *hub*  
- Káº¿t ná»‘i giá»¯a cÃ¡c microservice chá»‰ giá»›i háº¡n á»Ÿ ná»™i dung cá»§a message Ä‘Æ°á»£c xuáº¥t  
- Giáº£m sá»‘ lÆ°á»£ng synchronous calls vÃ¬ pub/sub lÃ  *push* khÃ´ng Ä‘á»“ng bá»™ má»™t chiá»u

NhÆ°á»£c Ä‘iá»ƒm: cáº§n **phá»‘i há»£p vÃ  giÃ¡m sÃ¡t** Ä‘á»ƒ trÃ¡nh microservice xá»­ lÃ½ nháº§m message.

---

## Core microservice

Cung cáº¥p dá»¯ liá»‡u ná»n táº£ng vÃ  lá»›p truyá»n thÃ´ng, gá»“m:  
- **Amazon S3** bucket cho dá»¯ liá»‡u  
- **Amazon DynamoDB** cho danh má»¥c dá»¯ liá»‡u  
- **AWS Lambda** Ä‘á»ƒ ghi message vÃ o data lake vÃ  danh má»¥c  
- **Amazon SNS** topic lÃ m *hub*  
- **Amazon S3** bucket cho artifacts nhÆ° mÃ£ Lambda

> Chá»‰ cho phÃ©p truy cáº­p ghi giÃ¡n tiáº¿p vÃ o data lake qua hÃ m Lambda â†’ Ä‘áº£m báº£o nháº¥t quÃ¡n.

---

## Front door microservice

- Cung cáº¥p API Gateway Ä‘á»ƒ tÆ°Æ¡ng tÃ¡c REST bÃªn ngoÃ i  
- XÃ¡c thá»±c & á»§y quyá»n dá»±a trÃªn **OIDC** thÃ´ng qua **Amazon Cognito**  
- CÆ¡ cháº¿ *deduplication* tá»± quáº£n lÃ½ báº±ng DynamoDB thay vÃ¬ SNS FIFO vÃ¬:
  1. SNS deduplication TTL chá»‰ 5 phÃºt
  2. SNS FIFO yÃªu cáº§u SQS FIFO
  3. Chá»§ Ä‘á»™ng bÃ¡o cho sender biáº¿t message lÃ  báº£n sao

---

## Staging ER7 microservice

- Lambda â€œtriggerâ€ Ä‘Äƒng kÃ½ vá»›i pub/sub hub, lá»c message theo attribute  
- Step Functions Express Workflow Ä‘á»ƒ chuyá»ƒn ER7 â†’ JSON  
- Hai Lambda:
  1. Sá»­a format ER7 (newline, carriage return)
  2. Parsing logic  
- Káº¿t quáº£ hoáº·c lá»—i Ä‘Æ°á»£c Ä‘áº©y láº¡i vÃ o pub/sub hub

---

## TÃ­nh nÄƒng má»›i trong giáº£i phÃ¡p

### 1. AWS CloudFormation cross-stack references
VÃ­ dá»¥ *outputs* trong core microservice:
```yaml
Outputs:
  Bucket:
    Value: !Ref Bucket
    Export:
      Name: !Sub ${AWS::StackName}-Bucket
  ArtifactBucket:
    Value: !Ref ArtifactBucket
    Export:
      Name: !Sub ${AWS::StackName}-ArtifactBucket
  Topic:
    Value: !Ref Topic
    Export:
      Name: !Sub ${AWS::StackName}-Topic
  Catalog:
    Value: !Ref Catalog
    Export:
      Name: !Sub ${AWS::StackName}-Catalog
  CatalogArn:
    Value: !GetAtt Catalog.Arn
    Export:
      Name: !Sub ${AWS::StackName}-CatalogArn


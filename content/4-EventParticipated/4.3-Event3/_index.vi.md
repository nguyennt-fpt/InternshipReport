---
title: "Event 3"
date: "2025-11-15"
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# Bài thu hoạch: AI/ML/GenAI on AWS

### Mục đích của sự kiện

- Giới thiệu tổng quan AI/ML và GenAI trên AWS.

- Giúp người tham dự hiểu và thực hành với Sagemaker và Bedrock.

- Hướng dẫn xây dựng giải pháp GenAI thực tế như rag và chatbot.

### Danh sách diễn giả

- **AWS GenAI Specialist Team**  
- **AWS Solutions Architects**  
- **Guest AI Practitioners from industry**


### Nội dung nổi bật

#### Kỹ thuật prompting cho LLMs

- **Zero shot prompting**: mô hình tự suy luận không cần ví dụ  
- **Few shot prompting**: dùng một vài ví dụ để tăng độ chính xác và tính nhất quán  
- **Chain of thought prompting**: hướng dẫn mô hình suy nghĩ theo từng bước để cải thiện lập luận  

#### Retrieval Augmented Generation (RAG)

- Giải thích cơ chế RAG  
- Các RAG use cases: chatbot doanh nghiệp, tra cứu tài liệu, trợ lý phân tích báo cáo, hỗ trợ kỹ thuật  
- Cách xây dựng RAG

#### Embedding và Amazon Titan Embeddings

- **Embedding**: biểu diễn dữ liệu dạng vector giúp mô hình hiểu ngữ nghĩa  
- Ứng dụng embedding trong search, clustering, semantic similarity, RAG  
- **Amazon Titan Embedding**: độ chính xác cao, tối ưu cho RAG, hỗ trợ multi-language

#### Amazon Bedrock

- Bedrock là nền tảng GenAI fully managed của AWS  
- Hỗ trợ nhiều foundation models và Titan models  
- Cung cấp API thống nhất, bảo mật dữ liệu ở cấp doanh nghiệp  
- Tích hợp sẵn features cho Guardrails, RAG, Memory và Agents  

#### Agentic AI

- AI đang tiến hóa từ mô hình trả lời thụ động sang agentic AI, có khả năng:  
  - Lập kế hoạch (planning)  
  - Hành động theo mục tiêu (goal-oriented actions)  
  - Gọi các công cụ (tool calling)  
  - Giao tiếp với nhiều hệ thống  

#### Bedrock Agent Core

- Bedrock Agent Core: kiến trúc agent-centric để chạy nhiều agents ở quy mô lớn  
- Hỗ trợ:  
  - Routing between agents  
  - Tool calling orchestration  
  - Retrieval integration  
  - Guardrails at runtime  

#### Agent Core service enabling agents at scale

- Cho phép triển khai agents với tính năng:  
  - High availability  
  - Scalable worker orchestration  
  - Built-in monitoring và governance  
  - Dễ dàng tích hợp với hệ thống doanh nghiệp  

### Những gì học được

#### Prompting

- Zero shot phù hợp với tác vụ đơn giản  
- Few shot cải thiện độ chính xác trong các tác vụ phức tạp  
- Chain of thought giúp mô hình giải thích và lập luận tốt hơn cho bài toán logic  

#### Bedrock and Agentic AI

- Bedrock giúp doanh nghiệp triển khai GenAI mà không phải quản lý hạ tầng  
- Agentic AI là tương lai của ứng dụng AI trong doanh nghiệp  
- Bedrock Agent Core đơn giản hóa quá trình tạo, quản lý và mở rộng AI agents  

### Ứng Dụng Vào Công Việc

- Sử dụng Zero shot, Few shot và Chain of thought để cải thiện chất lượng prompting  

### Trải nghiệm trong event

Tham dự sự kiện mang lại góc nhìn mới về cách xây dựng hệ thống AI hiện đại, từ kỹ thuật prompting cho đến RAG và agents.

#### Học được từ những chuyên gia

- Các chuyên gia giải thích rõ kiến trúc prompting, embedding và RAG  
- Các case study thực tế cho thấy doanh nghiệp đang ứng dụng GenAI vào vận hành và tự động hóa  

#### Hands-on demonstrations

- Demo trực tiếp về xây dựng 1 Generative AI chatbot sử dụng Bedrock

#### Lessons learned

- Prompting tốt quyết định chất lượng output   
- Agentic AI là hướng đi tất yếu cho automation nâng cao  
- Bedrock Agent Core giúp xây dựng agents mạnh, bảo mật và dễ mở rộng  

> Sự kiện đã mở rộng tư duy của tôi về GenAI, đặc biệt là tính hữu dụng của các AI agents thông minh và ứng dụng chúng vào quy trình doanh nghiệp.


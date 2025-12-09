---
title: "Event 4"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 4.4. </b> "
---



**Summary Report: Attending AWS Cloud Mastery Series #1 - AI/ML/GenAI on AWS**

**Attendance Objectives**
Recently, I had the opportunity to attend the opening event for the "AWS Cloud Mastery" series, focusing on AI, Machine Learning, and Generative AI. My main goal was to update my comprehensive view of these technologies on the AWS platform and learn how to apply them to real-world business problems.

**Speakers**
The session featured sharing from experienced experts in the industry, including Mr. Lam Tuan Kiet (Sr DevOps Engineer - FPT Software), Mr. Danh Hoang Hieu Nghi (AI Engineer - Renova Cloud), Mr. Dinh Le Hoang Anh (Cloud Engineer Trainee), and Mr. Van Hoang Kha (Community Builder).

**Valuable Knowledge I Harvested:**

**1. The Power of Generative AI on Amazon Bedrock**
This was the part that impressed me the most. Amazon Bedrock acts as a central platform, providing access to leading Foundation Models (FMs) from Anthropic, OpenAI, Meta, etc. This allows us to fine-tune existing models without having to build a model from scratch.

I also reinforced my skills in **Prompt Engineering**, understanding better how to guide the model through various strategies:

- _Zero-shot:_ Providing a request directly without examples.
- _Few-shot:_ Providing a handful of examples for the model to mimic.
- _Chain-of-Thought:_ Asking the model to explain its reasoning steps for a more logical result.

Specifically, the **RAG (Retrieval Augmented Generation)** technique was highlighted as an optimal solution to improve accuracy:

- _Retrieval:_ Pulling real data from the enterprise knowledge base.
- _Augmentation:_ Adding that data as context for the prompt.
- _Generation:_ The model answers based on factual information, minimizing hallucinations.

Additionally, **Amazon Titan Embeddings** was introduced as a tool to convert text into vectors, serving semantic search and multilingual RAG workflows.

**2. AWS AI Services Ecosystem**
Beyond GenAI, I also reviewed AWS's "ready-made" AI services (APIs) that help solve specific problems quickly without complex model training:

- Image/Video Analysis (Rekognition).
- Translation (Translate) and Speech-to-Text/Text-to-Speech (Transcribe, Polly).
- Data Extraction (Textract) and Natural Language Processing (Comprehend).
- Intelligent Search (Kendra) or Anomaly Detection (Lookout).

The _AMZPhoto_ face recognition demo visually illustrated how to integrate these services into a real product.

**3. Amazon Bedrock AgentCore – Putting AI Agents into Practice**
This is a new framework helping to solve the problem of operating AI Agents at scale (production-ready). It supports long-term memory management, identity security, tool integration (like browsers, code interpreters), and most importantly, observability. This makes deploying frameworks like CrewAI or LangGraph safer and more effective on the AWS platform.

**Plan for Application at Work**
Based on what I learned, I plan to apply the following knowledge immediately:

- **Deploy RAG & AgentCore:** Propose and apply these to upcoming internal projects requiring GenAI features to increase accuracy and automation capabilities.
- **Optimize Development Process:** Use available AWS AI Services instead of building from scratch to shorten Time-to-market.
- **Improve Model Performance:** Apply advanced Prompt Engineering techniques to optimize output results for current AI tasks.

**Side Experience**
Not only did I absorb knowledge, but the event atmosphere was also extremely lively. I was lucky enough to reach the **Top 6** in the Kahoot competition; however, I dropped off the leaderboard during the final questions. Nevertheless, it was still an interesting game that helped me reinforce the knowledge from this event.

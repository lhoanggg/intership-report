---
title: "Lambda & API Gateway"
date: "2025-09-08"
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

In this section, you will create AWS Lambda functions and expose them via Amazon API Gateway to build the serverless backend for DaiVietBlood.

#### Architecture Overview

![Ảnh đại diện của bạn](../../images/5-Workshop/lambda.jpg)

#### API Endpoints

| Method | Endpoint | Description |
|:-------|:---------|:------------|
| GET | /users | Get all users |
| POST | /users | Create new user |
| GET | /users/{id} | Get user by ID |
| GET | /donations | Get all donations |
| POST | /donations | Create donation appointment |
| GET | /emergency-requests | Get emergency requests |
| POST | /emergency-requests | Create emergency request |

#### Content

1. [Create Lambda Functions](5.4.1-prepare/)
2. [Create API Gateway](5.4.2-create-interface-enpoint/)
3. [Test API Endpoints](5.4.3-test-endpoint/)
4. [Configure CORS & Security](5.4.4-dns-simulation/)

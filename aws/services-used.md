# AWS Services Used – ReceiptSnap (DevOps & Cloud Architecture)

This document describes the AWS services used in the **ReceiptSnap** project
from a **DevOps, Cloud Infrastructure, and Security** perspective.

The architecture is designed to be **scalable, secure, asynchronous, and production-ready**.

---

## 1. Amazon EC2 (Elastic Compute Cloud)

- Hosts the **containerized backend application**
- Docker runs the Node.js / Express API on EC2
- EC2 acts as the **primary backend service**
- An IAM Role is attached to EC2 to avoid hardcoding AWS credentials

### Responsibilities:
- Authentication & authorization
- Generate S3 presigned URLs
- Push processing jobs to SQS
- Receive processing callbacks
- Update database records

### Security:
- IAM Role-based access (no AWS keys stored)
- Security Groups restrict inbound traffic
- Logs and metrics sent to CloudWatch

---

## 2. Amazon S3 (Simple Storage Service)

Used for storing receipt files and processed outputs.

### Buckets / Structure:
- **Raw Uploads Bucket**
  - Stores user-uploaded receipt images or PDFs
- **Processed Bucket**
  - Stores OCR and AI-processed results (JSON / structured output)

### Access Pattern:
- Files uploaded using **presigned URLs**
- Backend and Lambda access S3 using IAM permissions

### Benefits:
- Secure file uploads
- No backend load during uploads
- Scalable and cost-efficient storage

---

## 3. Amazon SQS (Simple Queue Service)

Used for **asynchronous background processing**.

### Usage:
- Backend sends a processing job message to SQS
- Lambda function consumes messages from the queue
- Decouples API requests from heavy processing logic

### Benefits:
- Improves system responsiveness
- Handles traffic spikes gracefully
- Enables retry mechanisms and fault tolerance

---

## 4. AWS Lambda

Used for **receipt processing and AI orchestration**.

### Trigger:
- Invoked automatically by SQS messages

### Responsibilities:
- Fetch uploaded files from S3
- Perform OCR using Amazon Textract
- Process extracted text using Amazon Bedrock
- Store processed output back to S3
- Update processing status via callback API

### Security:
- Lambda uses IAM Role permissions
- No credentials stored in code or environment variables

---

## 5. Amazon Textract

Used for **OCR (Optical Character Recognition)**.

### Usage:
- Extracts text, tables, and key-value pairs from receipts
- Supports both images and PDFs
- Provides structured output for downstream processing

### Integration:
- Called from Lambda
- Output passed to Amazon Bedrock for AI enrichment

---

## 6. Amazon Bedrock

Used for **AI-powered data processing and understanding**.

### Usage:
- Processes Textract output using foundation models
- Converts raw OCR data into structured, meaningful insights
- Enables intelligent receipt classification and summarization

### Key Notes:
- No API keys are required
- Access is controlled via IAM permissions
- Requires `bedrock:InvokeModel` permission
- Region must support Amazon Bedrock (e.g. `us-east-1`)

---

## 7. IAM (Identity and Access Management)

IAM is used to securely manage access across all AWS services.

### IAM Roles:
- **EC2 Role**
  - Access to S3 (Get/Put)
  - Send messages to SQS
  - Write logs to CloudWatch
- **Lambda Role**
  - Access S3 objects
  - Read/Delete SQS messages
  - Invoke Textract and Bedrock
  - Write logs to CloudWatch

### Benefits:
- No hardcoded credentials
- Principle of least privilege
- Secure service-to-service communication

---

## 8. AWS Secrets Manager

Used to store and manage sensitive configuration data.

### Stored Secrets:
- Database credentials
- Application secrets (JWT, auth keys)

### Access:
- Secrets retrieved at runtime using IAM permissions
- Prevents exposure of secrets in source code or CI/CD pipelines

---

## 9. Amazon CloudWatch

Used for **logging, monitoring, and observability**.

### Usage:
- Application logs from EC2
- Execution logs from Lambda
- Error tracking and debugging
- Performance monitoring

---

## 10. CI/CD & DevOps Integration

### Tools:
- GitHub Actions for CI/CD
- Docker for containerization
- Docker Hub as container registry
- EC2 runner for deployment

### Flow:
1. Code pushed to GitHub repository
2. CI workflow builds Docker image
3. Image pushed to Docker Hub
4. CD workflow pulls image on EC2
5. Container deployed with runtime secrets

### Security:
- Secrets stored in GitHub Actions Secrets
- No sensitive data committed to repository

---

## Summary

ReceiptSnap leverages AWS managed services to build a **secure, scalable, and production-grade system**.

Key architectural principles:
- Asynchronous processing
- IAM-based security
- No hardcoded credentials
- AI-powered document processing
- Clean DevOps automation

This architecture is suitable for **real-world production workloads and system design interviews**.

# AWS Services Used – ReceiptSnap

This document lists the AWS services used in the ReceiptSnap project
from a DevOps and Cloud infrastructure perspective.

---

## EC2 (Elastic Compute Cloud)
- Used to host the containerized backend application
- Docker runs the backend service on an EC2 instance
- Security groups configured to allow HTTP access

---

## S3 (Simple Storage Service)
- Used for storing uploaded receipt files
- Separate buckets/folders used for:
  - Raw uploads
  - Processed outputs
- Files accessed using presigned URLs

---

## SQS (Simple Queue Service)
- Used for asynchronous job processing
- Backend sends processing jobs to SQS
- Helps decouple request handling from background processing

---

## AWS Lambda
- Triggered by SQS messages
- Processes uploaded receipt data
- Stores processed results back to S3

---

## IAM (Identity and Access Management)
- IAM roles attached to EC2 and Lambda
- Controlled access to S3 and SQS
- Avoided hardcoding AWS credentials

---

## Additional Services
- AWS Textract for OCR processing
- AWS Bedrock for AI-powered data processing

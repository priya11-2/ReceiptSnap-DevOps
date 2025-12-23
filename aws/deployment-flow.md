# AWS Deployment Flow – ReceiptSnap

This document explains how the application is deployed and runs
on AWS infrastructure.

---

## 1. Container Deployment on EC2
- Backend application is packaged as a Docker image
- Image is pushed to Docker Hub using CI pipeline
- EC2 instance pulls the latest Docker image
- Container runs on EC2 and exposes the application via public IP

---

## 2. File Upload & Storage
- Android client requests a presigned S3 URL
- Files are uploaded directly to S3
- Backend stores file metadata in the database

---

## 3. Asynchronous Processing
- Backend sends a processing message to SQS
- SQS triggers AWS Lambda
- Lambda fetches files from S3
- OCR and AI processing is performed
- Processed data is stored back in S3

---

## 4. Result Handling
- Backend receives processing callback
- Database is updated with processed results
- Client can fetch processed data via API

---

## 5. Security & Access
- IAM roles control access between services
- No AWS secrets are committed to the repository

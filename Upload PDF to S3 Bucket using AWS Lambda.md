# Upload PDF to S3 Bucket using AWS Lambda

This project provides an AWS Lambda function written in Python to upload a PDF or document file to an S3 bucket. The function decodes a Base64-encoded file and uploads it to the specified bucket. This guide will help you deploy and run the project.

---

## Prerequisites

Before setting up this project, ensure you have the following:

- **AWS Account**: Active AWS account with access to IAM, S3, and Lambda services.
- **AWS CLI**: Installed and configured with proper credentials (`aws configure`).
- **Python**: Python 3.7 or above installed locally.
- **Boto3**: AWS SDK for Python (install using `pip install boto3`).
- **Permissions**: The IAM role associated with the Lambda function must have access to S3.

---

## Steps to Deploy and Run

### 1. Clone the Repository

Clone the project repository to your local machine:

```bash
git clone <repository-url>
cd <repository-folder>
```

---

### 2. Create an S3 Bucket

1. Log in to the AWS Management Console.
2. Navigate to **S3** → **Create Bucket**.
3. Provide a bucket name (e.g., `my-lambda-upload-bucket`).
4. Choose the AWS region (e.g., `ap-south-1`).
5. Click **Create Bucket**.

---

### 3. Set Up IAM Role

Create an IAM role that grants permissions for Lambda to upload files to the S3 bucket:

1. Open the IAM Console.
2. Go to **Roles** → **Create Role**.
3. Choose **Lambda** as the trusted service.
4. Attach the `AmazonS3FullAccess` policy or create a custom policy with permissions like:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "s3:PutObject",
         "Resource": "arn:aws:s3:::my-lambda-upload-bucket/*"
       }
     ]
   }
   ```
5. Name the role (e.g., `LambdaS3UploadRole`) and save it.

---

### 4. Deploy the Lambda Function

#### Via AWS Console:

1. Open the AWS Lambda Console.
2. Click **Create Function** → **Author from Scratch**.
3. Provide a function name (e.g., `UploadPDFtoS3`).
4. Select Python 3.9 as the runtime.
5. Choose the IAM role created earlier.
6. Copy the contents of the `lambda_function.py` file from this repository into the inline code editor.
7. Add the necessary environment variables if needed.
8. Save the function.

#### Via AWS CLI:

1. Zip the function file:
   ```bash
   zip function.zip lambda_function.py
   ```
2. Deploy the function:
   ```bash
   aws lambda create-function \
     --function-name UploadPDFtoS3 \
     --runtime python3.9 \
     --role arn:aws:iam::ACCOUNT_ID:role/LambdaS3UploadRole \
     --handler lambda_function.lambda_handler \
     --zip-file fileb://function.zip
   ```

Replace `ACCOUNT_ID` with your AWS account ID.

---

### 5. Test the Lambda Function

#### Sample Test Event

Use the following JSON as a test event in the Lambda Console:

```json
{
  "fileName": "example.pdf",
  "fileContent": "BASE64_ENCODED_CONTENT",
  "bucketName": "my-lambda-upload-bucket"
}
```

Replace `BASE64_ENCODED_CONTENT` with the Base64 string of your file content.

#### Testing:

1. Open the Lambda function.
2. Go to the **Test** tab.
3. Paste the JSON test event.
4. Click **Test**.
5. Verify the file is uploaded to the specified S3 bucket.

---

### 6. Troubleshooting

- **Timeout Errors**: Increase the function timeout in the Lambda settings (Configuration → General Configuration).
- **Permissions Issues**: Ensure the IAM role has the correct permissions for the S3 bucket.
- **Incorrect Hostname/IP Errors**: Follow AWS S3 bucket naming conventions (avoid dots in the bucket name).

---

## Cleanup

To avoid incurring charges, delete the resources created:

1. Delete the Lambda function.
2. Delete the S3 bucket.
3. Remove the IAM role.

---

## License

This project is open-source under the MIT License.

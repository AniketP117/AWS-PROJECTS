# Upload PDF to S3 Bucket using AWS Lambda

This project demonstrates how to create an AWS Lambda function in Node.js to upload a PDF or document file to an Amazon S3 bucket. The function decodes a Base64-encoded file and uploads it to the specified bucket. Follow the guide below to deploy and execute the project successfully.

---

## Prerequisites

Before starting, make sure you have the following tools and permissions set up:

- **AWS Account**: An active AWS account with access to IAM, Lambda, and S3 services.
- **AWS CLI**: Installed and configured with valid AWS credentials (`aws configure`).
- **Node.js**: Version 14.x or above installed locally.
- **npm**: Comes with Node.js; used for managing dependencies.
- **AWS SDK for Node.js**: Installed via npm (`npm install aws-sdk`).
- **Permissions**: An IAM role attached to the Lambda function with access to S3.

---

## Steps to Deploy and Run

### 1. Clone the Repository

Download the repository to your local machine:

```bash
git clone <repository-url>
cd <repository-folder>
```

---

### 2. Install Dependencies

Install the required dependencies for the Lambda function:

```bash
npm install
```

---

### 3. Create an S3 Bucket

To store uploaded files, create a new S3 bucket:

1. Log in to the AWS Management Console.
2. Go to the **S3** service.
3. Click **Create Bucket** and provide a unique bucket name (e.g., `pdf-upload-bucket-node`).
4. Choose a preferred AWS region.
5. Complete the setup and create the bucket.

---

### 4. Configure IAM Role

Create an IAM role that allows Lambda to write to your S3 bucket:

1. Navigate to **IAM** in the AWS Management Console.
2. Select **Roles** and click **Create Role**.
3. Choose **Lambda** as the trusted service.
4. Attach the `AmazonS3FullAccess` policy or define a custom policy like this:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::pdf-upload-bucket-node/*"
    }
  ]
}
```
![IAM Policies](https://github.com/AniketP117/AWS-PROJECTS/blob/f84ea6d6b3164872b965a02abac59d0d04a360bc/IAM%20Policies.png)


5. Save the role as `LambdaS3UploadRole` or any name you prefer.

---

### 5. Deploy the Lambda Function

#### Using AWS Console:

1. Open the **AWS Lambda Console**.
2. Click **Create Function** → **Author from Scratch**.
3. Enter a function name (e.g., `UploadPDFtoS3`).
4. Set the runtime to Node.js 14.x.
5. Attach the IAM role created earlier.
6. Paste the code from the `index.js` file (from this repository) into the inline code editor.
7. Save the function.
![Alt Text](https://github.com/AniketP117/AWS-PROJECTS/blob/a451e7aeb0b198c8c1d3fccc1958dde8311842cb/Lambda%20Main%20Page.png)


#### Using AWS CLI:

1. Compress the function and its dependencies into a ZIP file:

```bash
zip -r function.zip index.js node_modules
```

2. Deploy the Lambda function:

```bash
aws lambda create-function \
  --function-name UploadPDFtoS3Node \
  --runtime nodejs14.x \
  --role arn:aws:iam::ACCOUNT_ID:role/LambdaS3UploadRole \
  --handler index.handler \
  --zip-file fileb://function.zip
```

Replace `ACCOUNT_ID` with your AWS account ID.

---

### 6. Code and Implementation

#### Code

Add your Lambda function code here.

```javascript
// Sample code for uploading to S3 in Node.js
const { S3Client, PutObjectCommand } = require('@aws-sdk/client-s3');

// Configure the S3 client
const s3 = new S3Client({
  region: 'ap-south-1', // Replace with your bucket's region
});

exports.handler = async (event) => {
  try {
    // Validate input parameters
    if (!event.fileName || !event.fileContent || !event.bucketName) {
      throw new Error('Missing required parameters: fileName, fileContent, or bucketName.');
    }

    const { fileName, fileContent, bucketName } = event;

    // Determine the ContentType based on the file extension
    const fileExtension = fileName.split('.').pop().toLowerCase();
    let contentType;

    switch (fileExtension) {
      case 'txt':
        contentType = 'text/plain';
        break;
      case 'pdf':
        contentType = 'application/pdf';
        break;
      default:
        throw new Error('Unsupported file type. Only TXT and PDF files are allowed.');
    }

    // Decode Base64 file content
    const fileBuffer = Buffer.from(fileContent, 'base64');

    // Define S3 upload parameters
    const params = {
      Bucket: bucketName, // Bucket name
      Key: fileName, // File name (key) in the bucket
      Body: fileBuffer, // File content
      ContentType: contentType, // MIME type based on file type
    };

    // Execute the PutObjectCommand to upload the file
    const command = new PutObjectCommand(params);
    const result = await s3.send(command);

    // Return success response
    return {
      statusCode: 200,
      body: JSON.stringify({
        message: 'File uploaded successfully!',
        fileUrl: `https://${bucketName}.s3.${s3.config.region}.amazonaws.com/${fileName}`,
        result,
      }),
    };
  } catch (error) {
    // Return failure response with error details
    return {
      statusCode: 500,
      body: JSON.stringify({
        message: 'File upload failed.',
        error: error.message,
      }),
    };
  }
};
```

---
![Alt Text](https://github.com/AniketP117/AWS-PROJECTS/blob/ff49a2306f0af60e58a4f2aeae47c4e648931790/Code%20Implementation%20Page.png)


### 7. Test and Results

#### Test Event

Prepare a test event in JSON format as shown below:

```json
{
  "fileName": "example.pdf",
  "fileContent": "BASE64_ENCODED_CONTENT",
  "bucketName": "pdf-upload-bucket-node"
}
```
![Alt Text](https://github.com/AniketP117/AWS-PROJECTS/blob/968e7a502ac14703804e48c61957831bb993335e/Code%20Execution%20Page.png)

Replace `BASE64_ENCODED_CONTENT` with the Base64 string of your file content.

#### Execution

1. Open the **Test** tab in the Lambda Console.
2. Paste the JSON payload.
3. Execute the function by clicking **Test**.

#### Results

- Check the S3 bucket to confirm the file has been uploaded.
![Alt Text](https://github.com/AniketP117/AWS-PROJECTS/blob/13e9f9296b0ef785ad34e7392724c37f41bccf5a/S3%20Bucket%20Page.png)
- Successful uploads will return a response like:

```json
{
  "statusCode": 200,
  "body": "{\"message\":\"File uploaded successfully!\"}"
}
```
![Alt Text](https://github.com/AniketP117/AWS-PROJECTS/blob/1996659522b78c9c041b2a251a8361c128ed3be5/Executed%20Funtion%20Result.png)

- Errors will return a response like:

```json
{
  "statusCode": 500,
  "body": "{\"message\":\"File upload failed.\",\"error\":\"Error details\"}"
}
```

---

### 8. Troubleshooting

- **Timeout Errors**: If the file upload exceeds the default timeout, increase the timeout value in the Lambda configuration under **General Configuration**.
- **Permission Denied**: Ensure the IAM role has the correct permissions to access the specified S3 bucket.
- **Invalid File Name**: Ensure the file name is valid and does not contain special characters.

---

## Cleanup

To avoid unnecessary charges, clean up the resources after testing:

1. Delete the Lambda function from the **AWS Lambda Console**.
2. Remove the files or delete the S3 bucket.
3. Delete the IAM role created for the function.

---

## License

This project is open-source.

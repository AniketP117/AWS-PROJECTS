# Lambda Function for Addition and Returning Results

This project provides an AWS Lambda function written in Node.js that calculates the sum of two numbers. The function takes input values via an event payload, performs the addition, and returns the result in the response.

---

## Prerequisites

Before deploying and testing this function, ensure you have the following:

- **AWS Account**: Active AWS account with access to Lambda.
- **AWS CLI**: Installed and configured with valid credentials (`aws configure`).
- **Node.js**: Version 14.x or above installed locally.
- **Permissions**: IAM role with basic Lambda execution permissions.

---

## Function Overview

### Function Code

The function accepts two numbers as input parameters (`num1` and `num2`) and returns their sum. Below is the function code:

```javascript
exports.handler = async (event) => {
    const num1 = event.num1;
    const num2 = event.num2;
    const result = num1 + num2;

    return {
        statusCode: 200,
        body: JSON.stringify({ result }),
    };
};
```

### Sample Event Payload

To test the function, provide an event payload in the following format:

```json
{
  "num1": 10,
  "num2": 20
}
```

### Expected Response

The function will return a response with the result of the addition:

```json
{
  "statusCode": 200,
  "body": "{\"result\":30}"
}
```

---

## Steps to Deploy and Run

### 1. Create the Lambda Function

#### Using AWS Management Console

1. Log in to the AWS Management Console.
2. Go to the **Lambda** service.
3. Click **Create Function** → **Author from Scratch**.
4. Provide a function name (e.g., `AdditionFunction`).
5. Select **Node.js 14.x** as the runtime.
6. Attach an IAM role with basic Lambda execution permissions.
7. Copy and paste the function code into the inline editor.
8. Save the function.
![Alt Text](https://github.com/AniketP117/AWS-PROJECTS/blob/4113e876da23902a392d67349d8fc384761a888e/ADD%20TWO%20NUMBERS%20AND%20RETURN%20THE%20RESULTS/Lambda%20Home%20Page.png)

#### Using AWS CLI

1. Save the function code to a file named `index.js`.
2. Create a ZIP file:

```bash
zip function.zip index.js
```

3. Deploy the function using the CLI:

```bash
aws lambda create-function \
  --function-name AdditionFunction \
  --runtime nodejs14.x \
  --role arn:aws:iam::ACCOUNT_ID:role/ExecutionRole \
  --handler index.handler \
  --zip-file fileb://function.zip
```

Replace `ACCOUNT_ID` with your AWS account ID.

---

### 2. Test the Lambda Function

#### Test in Console

1. Open the function in the AWS Lambda Console.
2. Navigate to the **Test** tab.
3. Create a new test event with the following JSON:

```json
{
  "num1": 10,
  "num2": 20
}
```

4. Click **Test** to execute the function.
5. Verify the result in the execution logs or the returned response.

#### Test Using AWS CLI

Invoke the function using the CLI:

```bash
aws lambda invoke \
  --function-name AdditionFunction \
  --payload '{"num1": 10, "num2": 20}' \
  response.json
```

Check the `response.json` file for the output:

```json
{
  "statusCode": 200,
  "body": "{\"result\":30}"
}
```

---

## Cleanup

To avoid unnecessary charges, delete the Lambda function when it is no longer needed:

1. Go to the AWS Lambda Console.
2. Select the function and click **Delete**.

Alternatively, use the AWS CLI:

```bash
aws lambda delete-function --function-name AdditionFunction
```

---

## License

This project is licensed under the MIT License.

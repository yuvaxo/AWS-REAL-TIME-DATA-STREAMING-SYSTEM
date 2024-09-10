# Amazon AWS Real-Time Data Streaming Setup Guide

Welcome to the comprehensive guide on setting up real-time data streaming on Amazon Web Services (AWS). This guide will walk you through the essential steps required to establish a robust data streaming pipeline using various AWS services. 

This guide was meticulously developed by [K. Yuva Shankar](https://www.linkedin.com/in/yuva-shankar-4ba786228?utm_source=share&utm_campaign=share_via&utm_content=profile&utm_medium=android_app), leveraging extensive hands-on experience with AWS to create an efficient and scalable solution.

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Step 1: Create a Kinesis Data Stream](#step-1-create-a-kinesis-data-stream)
4. [Step 2: Create an S3 Bucket](#step-2-create-an-s3-bucket)
5. [Step 3: Create a Lambda Role](#step-3-create-a-lambda-role)
6. [Step 4: Create a Producer Lambda Function](#step-4-create-a-producer-lambda-function)
7. [Step 5: Set Up S3 Event Notification](#step-5-set-up-s3-event-notification)
8. [Step 6: Create Consumer Lambda Functions](#step-6-create-consumer-lambda-functions)
9. [Step 7: Upload a Text File to S3](#step-7-upload-a-text-file-to-s3)
10. [Step 8: Testing the Setup](#step-8-testing-the-setup)
11. [Clean-Up](#clean-up)
12. [Conclusion](#conclusion)

---

## Introduction

Real-time data streaming is a powerful method to ingest, process, and analyze data as it arrives in your system. AWS offers a suite of services that seamlessly integrate to provide a scalable and efficient real-time data processing solution. In this guide, you will learn how to set up a real-time data streaming pipeline using AWS Kinesis, S3, and Lambda.

## Prerequisites

Before you begin, ensure you have the following:

- An AWS account with appropriate permissions to create and manage Kinesis streams, S3 buckets, Lambda functions, and IAM roles.
- Basic knowledge of AWS services, particularly Kinesis, S3, and Lambda.
- Familiarity with AWS Management Console.

## Step 1: Create a Kinesis Data Stream

1. Sign in to the [AWS Management Console](https://aws.amazon.com/console/).
2. Navigate to **Amazon Kinesis** under the "Analytics" category.
3. Choose **Data Streams** and then click on **Create data stream**.
4. Provide a name for your stream, e.g., `MyDataStream`.
5. Specify the number of shards based on your throughput requirements. (Each shard can ingest up to 1 MB per second or 1,000 records per second.)
6. Click on **Create stream**. Your data stream will be ready in a few moments.

## Step 2: Create an S3 Bucket

1. Go to the **Amazon S3** service in the AWS Management Console.
2. Click on **Create bucket**.
3. Enter a unique name for your bucket, e.g., `my-data-bucket`.
4. Choose the region where you want to create your bucket.
5. Configure any additional settings as per your requirements, such as versioning, encryption, etc.
6. Click on **Create bucket** to finalize.

## Step 3: Create a Lambda Role

1. Navigate to the **IAM** service in the AWS Management Console.
2. Click on **Roles** and then **Create role**.
3. Choose **AWS service** as the trusted entity and select **Lambda** from the list of services.
4. Attach the necessary policies:
   - `AWSLambdaKinesisExecutionRole` for reading from Kinesis streams.
   - `AWSLambdaBasicExecutionRole` for logging.
   - `AmazonS3FullAccess` (or a more restrictive policy) to allow Lambda to interact with S3.
5. Name your role, e.g., `LambdaKinesisRole`, and click on **Create role**.

## Step 4: Create a Producer Lambda Function

1. In the AWS Management Console, go to the **Lambda** service.
2. Click on **Create function** and choose **Author from scratch**.
3. Provide a name for your function, e.g., `KinesisProducerLambda`.
4. Select **Python 3.x** or **Node.js** as the runtime.
5. Under **Permissions**, choose **Use an existing role** and select the role you created earlier (`LambdaKinesisRole`).
6. Write or paste the code that will produce data to the Kinesis stream. Here's a basic example in Python:

    ```python
    import boto3
    import json
    import os

    kinesis = boto3.client('kinesis')

    def lambda_handler(event, context):
        data = {
            'message': 'Hello, this is a sample data stream!'
        }
        response = kinesis.put_record(
            StreamName=os.environ['KINESIS_STREAM_NAME'],
            Data=json.dumps(data),
            PartitionKey='partitionKey'
        )
        return response
    ```

7. Set up environment variables for the function, particularly the `KINESIS_STREAM_NAME`.
8. Click on **Deploy** to save your function.

## Step 5: Set Up S3 Event Notification

1. Go to your S3 bucket in the AWS Management Console.
2. Navigate to the **Properties** tab and scroll down to **Event notifications**.
3. Click on **Create event notification**.
4. Provide a name for your event, e.g., `NewFileUpload`.
5. Specify the event type, such as **All object create events**.
6. Under **Destination**, choose **Lambda function** and select the consumer Lambda function that will process the data.
7. Save the configuration.

## Step 6: Create Consumer Lambda Functions

1. Repeat the process of creating a Lambda function as described in Step 4.
2. This time, your function should consume data from the Kinesis stream, process it, and potentially store the results in S3 or trigger another service.
3. Here’s an example in Python that reads from Kinesis:

    ```python
    def lambda_handler(event, context):
        for record in event['Records']:
            payload = record['kinesis']['data']
            print(f"Decoded payload: {payload}")
    ```

4. Deploy the function once you’re done.

## Step 7: Upload a Text File to S3

1. Prepare a sample text file on your local machine.
2. Upload this file to the S3 bucket you created earlier using the **Upload** option in the S3 Management Console.
3. Ensure that the file upload triggers the Lambda function via the event notification.

## Step 8: Testing the Setup

1. Monitor the execution of your Lambda functions in the **CloudWatch Logs**.
2. Verify that the producer Lambda is successfully writing data to the Kinesis stream.
3. Ensure that the consumer Lambda functions are processing the data as expected.
4. If everything is set up correctly, you should see logs indicating successful data ingestion and processing.

## Clean-Up

After completing your testing, it’s important to clean up the resources to avoid unnecessary charges.

1. **Delete the Kinesis Stream:**
   - Go to the Kinesis service and delete the stream you created.

2. **Delete the S3 Bucket and Objects:**
   - Empty the S3 bucket by deleting all objects, and then delete the bucket.

3. **Delete Lambda Functions:**
   - Navigate to the Lambda service and delete all functions you created.

4. **Delete the IAM Role:**
   - Go to the IAM service and delete the role you created for Lambda functions.

5. **Remove Any Associated Policies:**
   - Ensure that all custom policies attached to the role are removed.

## Conclusion

This guide provided a step-by-step approach to setting up a real-time data streaming pipeline using AWS Kinesis, S3, and Lambda. Following these steps ensures a scalable, efficient, and manageable solution for processing data in real-time. We hope this guide has been helpful in your journey to mastering AWS services for data streaming.

---

*Developed by [K. Yuva Shankar](https://www.linkedin.com/in/yuva-shankar-4ba786228?utm_source=share&utm_campaign=share_via&utm_content=profile&utm_medium=android_app).*

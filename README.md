# AMAZON AWS Real time Data Streaming (Guide)

**Introduction**
Welcome to the guide on how to set up data streams on AWS. This README provides an overview of the steps to be followed, developed by [K.YUVA SHANKAR](https://www.linkedin.com/in/yuva-shankar-4ba786228?utm_source=share&utm_campaign=share_via&utm_content=profile&utm_medium=android_app).


**Steps To be followed:**

1. **Create Kinesis Data Stream**
2. **Create S3 Bucket**
3. **Create Lambda Role**
4. **Create Producer Lambda**
5. **Create Event Notification**
6. **Create Consumer Lambdas**
7. **Upload Text File to S3**
8. **Testing**

**Clean Up Steps:**
   - Delete Kinesis stream, S3 file, bucket, Lambda functions, role, and policy.



**Introduction**

Real-time data streaming is a powerful method to ingest, process, and analyze data as it arrives in your system. AWS offers a suite of services that seamlessly integrate to provide a scalable and efficient real-time data processing solution. In this guide, you will learn how to set up a real-time data streaming pipeline using AWS Kinesis, S3, and Lambda.

**Prerequisites**

Before you begin, ensure you have the following:
An AWS account with appropriate permissions to create and manage Kinesis streams, S3 buckets, Lambda functions, and IAM roles.
Basic knowledge of AWS services, particularly Kinesis, S3, and Lambda.
Familiarity with AWS Management Console.

**Step 1: Create a Kinesis Data Stream**

Sign in to the AWS Management Console.
Navigate to Amazon Kinesis under the "Analytics" category.
Choose Data Streams and then click on Create data stream.
Provide a name for your stream, e.g., MyDataStream.
Specify the number of shards based on your throughput requirements. (Each shard can ingest up to 1 MB per second or 1,000 records per second.)
Click on Create stream. Your data stream will be ready in a few moments.

**Step 2: Create an S3 Bucket**

Go to the Amazon S3 service in the AWS Management Console.
Click on Create bucket.
Enter a unique name for your bucket, e.g., my-data-bucket.
Choose the region where you want to create your bucket.
Configure any additional settings as per your requirements, such as versioning, encryption, etc.
Click on Create bucket to finalize.

**Step 3: Create a Lambda Role**

Navigate to the IAM service in the AWS Management Console.
Click on Roles and then Create role.
Choose AWS service as the trusted entity and select Lambda from the list of services.
Attach the necessary policies:
AWSLambdaKinesisExecutionRole for reading from Kinesis streams.
AWSLambdaBasicExecutionRole for logging.
AmazonS3FullAccess (or a more restrictive policy) to allow Lambda to interact with S3.
Name your role, e.g., LambdaKinesisRole, and click on Create role.

**Step 4: Create a Producer Lambda Function**

In the AWS Management Console, go to the Lambda service.
Click on Create function and choose Author from scratch.
Provide a name for your function, e.g., KinesisProducerLambda.
Select Python 3.x or Node.js as the runtime.
Under Permissions, choose Use an existing role and select the role you created earlier (LambdaKinesisRole).

**Write or paste the code that will produce data to the Kinesis stream. Here's a basic example in Python**


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
Set up environment variables for the function, particularly the KINESIS_STREAM_NAME.

Click on Deploy to save your function.

**Step 5: Set Up S3 Event Notification**

Go to your S3 bucket in the AWS Management Console.
Navigate to the Properties tab and scroll down to Event notifications.
Click on Create event notification.
Provide a name for your event, e.g., NewFileUpload.
Specify the event type, such as All object create events.
Under Destination, choose Lambda function and select the consumer Lambda function that will process the data.
Save the configuration.

**Step 6: Create Consumer Lambda Functions**

Repeat the process of creating a Lambda function as described in Step 4.
This time, your function should consume data from the Kinesis stream, process it, and potentially store the results in S3 or trigger another service.

**Here’s an example in Python that reads from Kinesis:**


def lambda_handler(event, context):
    for record in event['Records']:
        payload = record['kinesis']['data']
        print(f"Decoded payload: {payload}")
Deploy the function once you’re done.

**Step 7: Upload a Text File to S3**

Prepare a sample text file on your local machine.
Upload this file to the S3 bucket you created earlier using the Upload option in the S3 Management Console.
Ensure that the file upload triggers the Lambda function via the event notification.

**Step 8: Testing the Setup**

Monitor the execution of your Lambda functions in the CloudWatch Logs.
Verify that the producer Lambda is successfully writing data to the Kinesis stream.
Ensure that the consumer Lambda functions are processing the data as expected.
If everything is set up correctly, you should see logs indicating successful data ingestion and processing.
Clean-Up

**After completing your testing, it’s important to clean up the resources to avoid unnecessary charges.**

**Delete the Kinesis Stream:**
Go to the Kinesis service and delete the stream you created.

**Delete the S3 Bucket and Objects:**
Empty the S3 bucket by deleting all objects, and then delete the bucket.

**Delete Lambda Functions:**
Navigate to the Lambda service and delete all functions you created.

**Delete the IAM Role:**
Go to the IAM service and delete the role you created for Lambda functions.

**Remove Any Associated Policies:**
Ensure that all custom policies attached to the role are removed.

**Conclusion**

This guide provided a step-by-step approach to setting up a real-time data streaming pipeline using AWS Kinesis, S3, and Lambda. Following these steps ensures a scalable, efficient, and manageable solution for processing data in real-time. We hope this guide has been helpful in your journey to mastering AWS services for data streaming.

---

*Developed by [K.YUVA SHANKAR](https://www.linkedin.com/in/yuva-shankar-4ba786228?utm_source=share&utm_campaign=share_via&utm_content=profile&utm_medium=android_app).
*

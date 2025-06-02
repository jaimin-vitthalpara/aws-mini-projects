
# 🛒 Real-Time E-Commerce Transaction Monitoring with AWS Kinesis

This project showcases how to simulate and monitor **real-time e-commerce transaction data** using **Amazon Kinesis**, **AWS Lambda**, **DynamoDB**, and **Amazon QuickSight** for live visualization. It is ideal for beginners who want to understand how AWS Kinesis works in a real-time streaming architecture.

## 🚀 Project Overview

The project simulates fake e-commerce transactions and streams them in real time to AWS services using **Amazon Kinesis Data Streams**. These transactions are processed by a **Lambda consumer**, stored in **DynamoDB**, and visualized with **Amazon QuickSight** dashboards.

This is a complete pipeline project that demonstrates real-time ingestion, processing, storage, and visualization of streaming data.

---

## 🔧 Tech Stack

- **Amazon Kinesis Data Stream** – Ingest real-time transaction data
- **AWS Lambda** – Process and transform the data from Kinesis to DynamoDB
- **Amazon DynamoDB** – Store the processed transaction data
- **Amazon QuickSight** – Visualize transaction metrics live
- **Python** – For generating fake transaction data using Faker
- **IAM Roles & Policies** – Provide access to resources
- **VS Code / AWS Lambda Console** – Run data producer script

---

## 🧠 Project Highlights

- Real-time data ingestion with **Kinesis**
- **Stateless Lambda processing** for each transaction event
- Fast NoSQL storage via **DynamoDB**
- Visual analytics via **QuickSight dashboards**
- Scalable and serverless architecture

---

## 🔑 Features

- Simulates fake transactions (user, product, amount, location, timestamp)
- Processes streaming data with Lambda
- Stores structured data in DynamoDB
- Offers QuickSight insights (e.g., total sales by location or product)
- Beginner-friendly and fully serverless

---

## 📋 Requirements

- AWS Account with:
  - Access to **Kinesis**, **Lambda**, **DynamoDB**, **IAM**, and **QuickSight**
- Python 3 installed (for running the data generator script)
- Install required Python packages:
```bash
pip install boto3 faker
```

---

## ⚙️ Setup Instructions

### 1. Create a Kinesis Data Stream

- Name: `EcommerceTransactionStream`
- Shard count: `1` (for testing purposes)

### 2. Create a DynamoDB Table

- Name: `EcommerceTransactions`
- Partition Key: `transaction_id` (String)

### 3. Create IAM Role for Lambda

- Name: `KinesisLambdaExecutionRole`
- Attach policies:
  - AWSLambdaBasicExecutionRole
  - AmazonKinesisFullAccess
  - AmazonDynamoDBFullAccess

---

## 🧪 Part 1: Python Data Producer

> Save as `generate_fake_data.py` and run in your terminal or use an EC2 instance.

```python
import boto3
import json
import random
import time
from faker import Faker

fake = Faker()
kinesis = boto3.client('kinesis', region_name='us-east-1')

def generate_transaction():
    return {
        'transaction_id': fake.uuid4(),
        'user_name': fake.name(),
        'product': random.choice(['Laptop', 'Mobile', 'Camera', 'Shoes', 'Watch']),
        'amount': round(random.uniform(50.0, 2000.0), 2),
        'location': fake.city(),
        'timestamp': fake.iso8601()
    }

while True:
    data = generate_transaction()
    kinesis.put_record(
        StreamName='EcommerceTransactionStream',
        Data=json.dumps(data),
        PartitionKey=data['transaction_id']
    )
    print(f"Sent: {data}")
    time.sleep(2)
```

---

## 🧪 Part 2: AWS Lambda Consumer

> Create a Lambda function and connect it to the Kinesis stream as an event source.

### Lambda Code:

```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('EcommerceTransactions')

def lambda_handler(event, context):
    for record in event['Records']:
        payload = json.loads(record['kinesis']['data'])
        table.put_item(Item=payload)
    return {"statusCode": 200, "body": "Success"}
```

### Notes:

- Attach the previously created **IAM role** to this function
- Add `EcommerceTransactionStream` as a **Kinesis trigger**

---

## 📊 Visualize with Amazon QuickSight

1. Go to QuickSight > Manage Data > New Dataset > DynamoDB
2. Select `EcommerceTransactions` table
3. Import to SPICE for faster performance
4. Create charts (e.g., Sales by Product, Sales Over Time)

---

## 📸 Screenshots

1. **Set-up Kinesis Data Stream**
![Set-up Kinesis Data Stream](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/53cd594adbb23f89455183c20f51049f98155da9/Kinesis_graph-1.png)

2. **IAM Policies**
![IAM Policies](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/53cd594adbb23f89455183c20f51049f98155da9/Kinesis-IAM-Policy-1.png)

3. **Python Fake Data Producer**
![Python Fake Data Producer](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/53cd594adbb23f89455183c20f51049f98155da9/Screenshot%20(28).png)

4. **Lambda Fun to store tha data in DynamoDB, Generate .CSV file to upload in S3**
![Lambda Fun to store tha data in DynamoDB, Generate .CSV file to upload in S3](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/53cd594adbb23f89455183c20f51049f98155da9/Kinesis-lambda-1.png)

5. **DynamoDB table To store real-time data from Python Fake data Producer**
![DynamoDB table To store real-time data from Python Fake data Producer](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/53cd594adbb23f89455183c20f51049f98155da9/Kinesis-DynamoDB-1.png)

6. **Amazon Quicksight via Upload S3 bucket**
![Amazon Quicksight via Upload S3 bucket](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/53cd594adbb23f89455183c20f51049f98155da9/Kinesis-Quicksight-1.png)

---

## 💡 Conclusion

This project helped me get hands-on with **Amazon Kinesis**, **AWS Lambda**, **DynamoDB**, and **QuickSight** for building a **real-time data pipeline**. It is beginner-friendly, scalable, and demonstrates the real-world use case of streaming analytics.

---

---

## 🤝 Let's Connect!

If you found this helpful, feel free to connect or DM me on [LinkedIn ~ Jaimin Vitthalpara](https://www.linkedin.com/in/jaimin-vitthalpara-291a6a14b) and let's talk about AWS & cloud projects 🚀

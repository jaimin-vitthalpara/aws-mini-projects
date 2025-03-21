# Serverless AWS Project: Author Management API

## Overview
This project implements a **serverless API** using AWS Lambda, API Gateway, IAM, and DynamoDB. It allows users to **store author details** in a DynamoDB table and retrieve them via API calls tested with Postman.

## AWS Services Used
- **AWS Lambda**: Executes the API logic.
- **API Gateway**: Routes HTTP requests to the Lambda function.
- **IAM**: Manages permissions for secure access.
- **DynamoDB**: Stores author data.
- **Postman**: Used for API testing.

## Project Architecture
1. **API Gateway** receives HTTP requests.
2. **AWS Lambda** processes the request.
3. **DynamoDB** stores/retrieves author data.
4. **Postman** is used for testing API responses.

## Lambda Function Code
```python
import json
import boto3
import uuid

def lambda_handler(event, context):
    try:
        # Parse the request body (if it exists)
        if 'body' in event:
            data = json.loads(event['body'])
        else:
            return {
                "statusCode": 400,
                "body": json.dumps({"message": "Missing request body"})
            }

        # Extract data from the request (adding author fields)
        first_name = data.get('first_name', 'No First Name')
        last_name = data.get('last_name', 'No Last Name')
        bio = data.get('bio', 'No Bio')
        email = data.get('email', 'No Email')
        nationality = data.get('nationality', 'No Nationality')
        books_written = data.get('books_written', [])

        # Generate a unique ID for the author
        author_id = str(uuid.uuid4())

        # Save the data to DynamoDB
        dynamodb = boto3.resource('dynamodb')
        table = dynamodb.Table('Authors')  # Assuming you're saving authors to this table

        table.put_item(
            Item={
                'author_id': author_id,
                'first_name': first_name,
                'last_name': last_name,
                'bio': bio,
                'email': email,
                'nationality': nationality,
                'books_written': books_written  # List of books written by the author
            }
        )

        return {
            "statusCode": 200,
            "body": json.dumps({
                "message": "Author added successfully",
                "author_id": author_id
            })
        }

    except Exception as e:
        return {
            "statusCode": 500,
            "body": json.dumps({
                "message": "Internal server error",
                "error": str(e)
            })
        }
```

## Steps to Deploy
1. **Create a Lambda Function**:
   - Navigate to AWS Lambda and create a new function.
   - Upload the Python code or paste it in the inline editor.
   - Assign the necessary IAM role permissions.

2. **Configure API Gateway**:
   - Create a new HTTP API in API Gateway.
   - Set up a POST method and link it to the Lambda function.

3. **Set Up DynamoDB Table**:
   - Create a table named `Authors` with `author_id` as the primary key.

4. **Test with Postman**:
   - Use Postman to send a POST request with JSON payload.
   - Verify the response and check the stored data in DynamoDB.

## Example API Request (Postman)
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "bio": "An accomplished author.",
  "email": "john.doe@example.com",
  "nationality": "American",
  "books_written": ["Book One", "Book Two"]
}
```

## Expected API Response
```json
{
  "message": "Author added successfully",
  "author_id": "123e4567-e89b-12d3-a456-426614174000"
}
```

## Conclusion
This **serverless application** provides a **scalable, cost-effective** way to manage author information using **AWS Lambda, API Gateway, and DynamoDB**. 🚀


## Screenshots

1. **Create a Lambda Function**
![Lambda function](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/5977a3b9bcd2cab3b10197682c16eeec6d59d55e/Author_Lmbda.png)

2. **API Gateway**
![API Gateway](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/5977a3b9bcd2cab3b10197682c16eeec6d59d55e/Author_API_gateway.png)

3. **Testing API using Postman**
![Postman](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/5977a3b9bcd2cab3b10197682c16eeec6d59d55e/Screenshot%20(9).png)

4. **Data Store in DynamoDB**
![DynamoDB](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/5977a3b9bcd2cab3b10197682c16eeec6d59d55e/Author_DB_Output.png)

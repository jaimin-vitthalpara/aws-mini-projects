
# WeatherBot: A Real-Time Weather Chatbot Using AWS

This is a **WeatherBot** project that provides **real-time weather updates** based on user input. The bot uses **Amazon Lex**, **AWS Lambda**, **OpenWeatherMap API**, and **Amazon DynamoDB** to deliver weather data in a friendly, conversational manner.

## 🚀 Project Overview

WeatherBot is a serverless chatbot application built on AWS that allows users to query the weather for different cities. By integrating **Amazon Lex** for natural language processing, **AWS Lambda** for backend processing, and **OpenWeatherMap API** for fetching live weather data, the bot responds with relevant weather details in a conversational format.

The bot also stores user queries and responses in **Amazon DynamoDB** for future reference.

## 🔧 Tech Stack

- **Amazon Lex**: For natural language understanding and managing conversational flows
- **AWS Lambda**: To process user queries and fetch data from OpenWeatherMap API
- **OpenWeatherMap API**: For retrieving real-time weather information
- **Amazon DynamoDB**: To store weather queries and responses for future reference
- **AWS CloudFormation**: To provision the infrastructure resources
- **AWS IAM**: For managing permissions to access AWS services

## 🧠 Project Highlights

- Implemented **serverless architecture** using AWS services.
- Designed **Lex intents** for handling user queries about weather.
- Integrated **OpenWeatherMap API** to get live weather data.
- Used **DynamoDB** to store weather queries and responses for analysis and history tracking.
- The chatbot simulates a thinking process with a friendly conversational tone (e.g., "Hmm, give me a sec..." before responding).
  
## 🔑 Features

- **Real-time weather updates** based on user input.
- **Friendly conversational responses** that simulate a natural interaction.
- Stores and retrieves user queries and responses in **DynamoDB** for tracking purposes.
- **End-to-end integration** using AWS Lambda and external APIs.

## 📋 Requirements

Before you can run this project, make sure you have:

- An **AWS Account** with access to **Amazon Lex**, **Lambda**, **DynamoDB**, and **IAM**.
- An **OpenWeatherMap API Key** to access weather data.

## ⚙️ Setup Instructions

### 1. Create an OpenWeatherMap API Key

- Visit [OpenWeatherMap](https://openweathermap.org/) and sign up.
- Get your **API Key** from the dashboard.

### 2. AWS Setup

1. **Create a DynamoDB table** named `WeatherBotTable` with the primary key `id`.
2. **Create a Lex Bot**:
    - Set up intents like `GetWeather`, `WelcomeIntent`, and `FallbackIntent` for handling user queries.
3. **Create a Lambda function** and configure the environment variables for the OpenWeatherMap API key.
4. **Add IAM permissions** to the Lambda function to allow access to DynamoDB and other necessary resources.
5. **Deploy your project** via AWS CloudFormation or manually, depending on your setup.

### 3. AWS Lambda Code

```python
import json
import urllib.request
import uuid
import boto3
from datetime import datetime

def lambda_handler(event, context):
    city = event['sessionState']['intent']['slots']['City']['value']['interpretedValue']
    api_key = "Enter Your Open Weather Map API Here.."
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}&units=metric"

    try:
        with urllib.request.urlopen(url) as response:
            data = json.loads(response.read())
            temp = data['main']['temp']
            condition = data['weather'][0]['main'].lower()
            description = data['weather'][0]['description']
            city_name = data['name']

            # Friendly message
            if "clear" in condition:
                mood = "☀️ It's a bright and sunny day!"
            elif "cloud" in condition:
                mood = "☁️ It's cloudy — maybe take a light jacket."
            elif "rain" in condition:
                mood = "🌧️ It's raining — don’t forget your umbrella!"
            elif "snow" in condition:
                mood = "❄️ Snowy today! Stay warm."
            elif "thunderstorm" in condition:
                mood = "⛈️ Thunderstorms outside — stay safe!"
            elif "mist" in condition or "fog" in condition:
                mood = "🌫️ Misty weather — drive carefully!"
            else:
                mood = "🌤️ Weather looks normal. Enjoy your day!"

            message = (
                        f"📍 Weather in {city_name}:\n"
                        f"🌡️ {temp}°C with {description}.\n"
                        f"{mood}"
            )


            # Save to DynamoDB
            dynamodb = boto3.resource('dynamodb')
            table = dynamodb.Table('WeatherBotTable')
            item = {
                'id': str(uuid.uuid4()),
                'city': city_name,
                'temperature': str(temp),
                'condition': condition,
                'description': description,
                'timestamp': datetime.utcnow().isoformat()
            }
            table.put_item(Item=item)

    except Exception as e:
        print("Error:", str(e))  # For CloudWatch Logs
        message = f"❌ Oops! I couldn't fetch the weather for '{city}'. Please try another city."

    return {
        "sessionState": {
            "dialogAction": {
                "type": "Close"
            },
            "intent": {
                "name": "GetWeather",
                "state": "Fulfilled"
            }
        },
        "messages": [
            {
                "contentType": "PlainText",
                "content": message
            }
        ]
    }

```

### 4. Run and Test the Bot

1. Open the **Amazon Lex console**, test the bot by typing queries like:
    - "What's the weather in Delhi?"
    - "Tell me the weather in Mumbai."
  
2. The bot should respond with the current weather details fetched from OpenWeatherMap and store the response in DynamoDB.

---

## 💡 Conclusion

This project helped me gain hands-on experience with **AWS Lambda**, **Amazon Lex**, **API integration**, **serverless architecture**, and **DynamoDB**. I look forward to applying these skills to more complex cloud-based applications in the future.

---

## 📸 Screenshot

1. **Lex-Chatbot**
![Lex-Chatbot](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/5cdbe4d560c5b0f4dc2a4459c63cbf5f8c8078a8/lex-chatbot.png)

2. **IAM Persmission**
![Lex-IAM Persmission](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/5cdbe4d560c5b0f4dc2a4459c63cbf5f8c8078a8/lex-IAM.png)

3. **ChatBot Lambda Func**
![Lex-Lambda](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/5cdbe4d560c5b0f4dc2a4459c63cbf5f8c8078a8/lex-lambda.png)

4. **ChatBot Data Store in DynamoDB**
![Lex-DynamoDB](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/5cdbe4d560c5b0f4dc2a4459c63cbf5f8c8078a8/lex-dynamoDB.png)

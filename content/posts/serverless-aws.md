---
title: "Building a Basic Web Application with AWS Amplify, API Gateway, Lambda and API Gateway"
date: 2023-08-24T11:17:33-05:00
tags: 
 - AWS
 - Projects
 - DevOps
---

This site is currently built using some serverless resources, specifically AWS S3, CloudFront, Route 53, Hugo and Github Actions. This was good for me because I did not want to write much front-end code, and using these technologies allowed me to focus on creating content. As I continue my journey to becoming a better engineer, I wanted to build a different site that I could use as a vehicle for learning additional technologies, without compromising this one. In other words, I wanted to build a test/dev site and not ruin my production site. 

AWS has a lab where we [Build a Basic Web Application](https://aws.amazon.com/getting-started/hands-on/build-web-app-s3-lambda-api-gateway-dynamodb/?ref=gsrchandson). This lab uses AWS Amplify, IAM, API Gateway, Lambda and DynamoDB to build a basic web app. I have not used these resources before so it will be good practice to learn how they work. Another step I would like to add is using Terraform to add versioning to this infrastructure.

To prepare for this, I have created a new AWS account to host all of the infrastructure. I have also created a new IAM admin user with MFA enabled. 

## Module 1: Create a Web App

We are leveraging AWS Amplify, which is a more straightforward way to deploy a static site. With my personal portfolio site, I used an S3 bucket to accomplish this. It will be interesting to see the differences between the two approaches. From what I understand, Amplify obscures the need to work with S3 directly and makes it easier to get up and running with a static site.

The first step is to create an index.html file, and zip it so that I can upload it to Amplify.

![1](/AWS-serverless/1-html.png)

Here are the settings I will be using:

![2](/AWS-serverless/2-amplify.png)

The deployment was successful.

![3](/AWS-serverless/3-deploymentsuccessful.png)

The site loads as expected! I can see the appeal of Amplify immediately.

![4](/AWS-serverless/4-helloworld.png)

## Module 2: Build a Serverless Function

Next, I will be building a Lambda function using Python. Here are the fields I filled out:

![5](/AWS-serverless/5-createlambda.png)

This is the code provided to us by AWS:

```python
# import the JSON utility package since we will be working with a JSON object
import json
# define the handler function that the Lambda service will use as an entry point
def lambda_handler(event, context):
# extract values from the event object we got from the Lambda service
    name = event['firstName'] +' '+ event['lastName']
# return a properly formatted JSON object
    return {
    'statusCode': 200,
    'body': json.dumps('Hello from Lambda, ' + name)
    }
```

Luckily, because I have been working on CS50's python course I can understand this code a bit. I looked at the documentation for the json.dumps method just to be sure I understand what it does. It converts an object to a string using a conversion table.

I then create a test event to test the Lambda function. It worked successfully!

![6](/AWS-serverless/6-lambdatest.png)

## Module 3: Link a Serverless Function to a Web App

The next step is to create the API Gateway, which will act as the middle layer between the user and the backend.


![7](/AWS-serverless/7-apigateway.png)

Next I define a method for POST calls to my API.

![8](/AWS-serverless/8-postmethod.png)

Then I enable CORS for my API.

![9](/AWS-serverless/9-cors.png)

Note: I did not actually understand what CORS was or why it was important. The idea behind CORS is that websites have resources that they use for their site, like images, videos, etc. If the site that owns that resource wants to use it, then there is typically no problem because the origin is the same. However, if another site wanted to use that resource, then it may not be allowed if the original site does not have CORS enabled. This is important for security, as without it, sites would be able to access your information without permission. I am no expert, but I can see why its important now and why it is necessary to enable it.

Next I test my lambda function. I am expecting it to return a status code and a statement saying "Hello from Lambda" plus the name that is passed in by the API.

![10](/AWS-serverless/10-deployapi.png)

![11](/AWS-serverless/11-lambdaworks.png)

The test was successful!

## Module 4: Create a Data Table


For this module, we will create a DynamoDB table and create an IAM user to allow our lambda function to talk to write to our DB.

First we create our table.

![12](/AWS-serverless/12-dynamodb.png)

Next we need to edit our Lambda function's permissions to enable access to read,edit and delete items for the specific table we specify.

```json
{
"Version": "2012-10-17",
"Statement": [
    {
        "Sid": "VisualEditor0",
        "Effect": "Allow",
        "Action": [
            "dynamodb:PutItem",
            "dynamodb:DeleteItem",
            "dynamodb:GetItem",
            "dynamodb:Scan",
            "dynamodb:Query",
            "dynamodb:UpdateItem"
        ],
        "Resource": "YOUR-TABLE-ARN"
    }
    ]
}
```
note: this is the code provided by AWS. In my code I have replaced "YOUR-TABLE-ARN" with the resource number for my example table.

![13](/AWS-serverless/13-dynamopolicy.png)

Next we need to adjust the code in our lambda function to actually do what we need it to do, which is access our DB table.

```python
# import the json utility package since we will be working with a JSON object
import json
# import the AWS SDK (for Python the package name is boto3)
import boto3
# import time 
import time
# import two packages to help us with dates and date formatting

# create a DynamoDB object using the AWS SDK
dynamodb = boto3.resource('dynamodb')
# use the DynamoDB object to select our table
table = dynamodb.Table('HelloWorldDatabase')

# define the handler function that the Lambda service will use as an entry point
def lambda_handler(event, context):
 # Get the current GMT time
    gmt_time = time.gmtime()

    # store the current time in a human readable format in a variable
    # Format the GMT time string
    now = time.strftime('%a, %d %b %Y %H:%M:%S +0000', gmt_time)


# extract values from the event object we got from the Lambda service and store in a variable
    name = event['firstName'] +' '+ event['lastName']
# write name and time to the DynamoDB table using the object we instantiated and save response in a variable
    response = table.put_item(
        Item={
            'ID': name,
            'LatestGreetingTime':now
            })
# return a properly formatted JSON object
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda, ' + name)
    }
```

note: make sure that the table resource you are calling in this line ```table = dynamodb.Table('HelloWorldDatabase')``` matches the name of the table you created.

Next I confirm that my Lambda function wrote persistent data to my DynamoDB table.

![14](/AWS-serverless/14-wrotedata.png)

## Module 5 - Add Interactivity to the Web App

Next I will update the code in the HTML document to the following:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Hello World</title>
    <!-- Add some CSS to change client UI -->
    <style>
    body {
        background-color: #232F3E;
        }
    label, button {
        color: #FF9900;
        font-family: Arial, Helvetica, sans-serif;
        font-size: 20px;
        margin-left: 40px;
        }
     input {
        color: #232F3E;
        font-family: Arial, Helvetica, sans-serif;
        font-size: 20px;
        margin-left: 20px;
        }
    </style>
    <script>
        // define the callAPI function that takes a first name and last name as parameters
        var callAPI = (firstName,lastName)=>{
            // instantiate a headers object
            var myHeaders = new Headers();
            // add content type header to object
            myHeaders.append("Content-Type", "application/json");
            // using built in JSON utility package turn object to string and store in a variable
            var raw = JSON.stringify({"firstName":firstName,"lastName":lastName});
            // create a JSON object with parameters for API call and store in a variable
            var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: raw,
                redirect: 'follow'
            };
            // make API call with parameters and use promises to get response
            fetch("YOUR-API-INVOKE-URL", requestOptions)
            .then(response => response.text())
            .then(result => alert(JSON.parse(result).body))
            .catch(error => console.log('error', error));
        }
    </script>
</head>
<body>
    <form>
        <label>First Name :</label>
        <input type="text" id="fName">
        <label>Last Name :</label>
        <input type="text" id="lName">
        <!-- set button onClick method to call function we defined passing input values as parameters -->
        <button type="button" onclick="callAPI(document.getElementById('fName').value,document.getElementById('lName').value)">Call API</button>
    </form>
</body>
</html>
```

This was a lot of additions, but it includes the functionality needed to call the lambda function. 

Next we add the new code to our Amplify deployment.

![15](/AWS-serverless/15-newdeployment.png)

Then we open the new deployment and test the functionality.

![16](/AWS-serverless/16-success!.png)

It works as expected! Now that it works, I will tear everything down so as not to incur any additional charges.

Takeaways:

Working with Lambda, API Gateway, DynamoDB and Amplify are all incredible services that can make developing much easier. While I am no expert, I can see the use case for each of these tools and the appeal of each.
Next, I want to work on adding additional DevOps processes to this project to improve it's ability to scale.
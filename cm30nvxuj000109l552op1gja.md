---
title: "Deploy a Node.js App on AWS Lambda with DynamoDB Using the Serverless Framework: A Step-by-Step Guide"
seoTitle: "Deploy a Serverless Node.js App on AWS Lambda with DynamoDB."
seoDescription: "Learn how to deploy a Node.js app on AWS Lambda using the Serverless Framework and connect it to DynamoDB. This beginner-friendly, step-by-step guide."
datePublished: Sat Nov 02 2024 21:14:38 GMT+0000 (Coordinated Universal Time)
cuid: cm30nvxuj000109l552op1gja
slug: deploy-a-nodejs-app-on-aws-lambda-with-dynamodb-using-the-serverless-framework-a-step-by-step-guide
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1730572486014/f5ba1f80-a49f-47ff-b02c-bd2c12b0273d.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1730581838180/44732f01-e120-4016-b7fa-a4d23bd4783a.webp
tags: cloud, aws, cloudfront, nodejs, apis, cloud-computing, devops, rest-api, serverless, aws-lambda, s3, devops-articles, lamda

---

Deploying applications in a serverless environment is becoming increasingly popular for its cost-effectiveness and ease of scaling. In this guide, you’ll learn how to deploy a Node.js application on AWS Lambda using the Serverless Framework, and connect it to DynamoDB. By the end, you’ll have a fully functional serverless app that interacts with DynamoDB, all with minimal setup and management!

---

## **1\. What is the Serverless Framework?**

The Serverless Framework is an open-source framework that helps developers manage serverless deployments on various cloud platforms, including AWS, Azure, and Google Cloud. It simplifies deploying serverless applications by allowing you to define resources and functions in a configuration file, eliminating the need for complex infrastructure setup.

---

## **2\. Prerequisites**

To follow along with this guide, ensure you have the following:

* **Node.js and npm**: Install the latest version of Node.js from [nodejs.org](http://nodejs.org).
    
* **AWS Account**: Set up an AWS account with access to Lambda and DynamoDB services. [Sign up here](https://aws.amazon.com/).
    
* **Serverless Framework**: Install it globally by running the command below if you haven’t already:
    
    ```bash
    npm install -g serverless
    ```
    

---

## **3\. Setting Up a New Serverless Project**

The first step is to create a Serverless project that will serve as the base for deploying your AWS resources and functions.

### **Steps:**

1. **Create the Project**  
    Open your terminal and run the following command:
    
    <div data-node-type="callout">
    <div data-node-type="callout-emoji">💡</div>
    <div data-node-type="callout-text">Make sure to create your account on serverless website and validate your credentials through terminal.</div>
    </div>
    
    ```bash
    serverless
    ```
    
    This will show you several Templates. Choose 3rd option Aws / Node.js / Express Api with Dynamodb
    
2. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730572964059/8cef1049-a935-424f-af00-b7d6d6644ba6.png align="center")
    
    Then, select your project name in that folder. The Serverless Framework will generate your complete boilerplate code
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730573046568/f62e9d7a-aa0e-4ffa-8fd2-1cd2f76a5c16.png align="center")
    
    3. Then select create a new app to continue.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730573068996/01768c07-532c-4a6a-83bc-a57f75495ca4.png align="center")
    
    4\. Next, provide an app name. A Lambda function will be created with this name
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730573997540/24c7645a-11e2-4357-95f2-f501372b555a.png align="center")
    
    5. **Install Dependencies**  
        All ready some dependencies are installed through boiler plate code and we have to add some more dependencies for this project
        
    
    ```bash
    cd "your project name"
    ```
    
    ```bash
    npm i
    ```
    
    ```bash
    npm i crypto-js
    npm i jsonwebtoken
    npm i cors
    ```
    
    The `aws-sdk` library is essential for integrating our app with AWS services.That is already installed if you selected aws + node.js+dynamodb template.
    

---

## **4\. Configuring** `serverless.yml` for Lambda and DynamoDB

The `serverless.yml` file defines your resources and functions. Here’s how to configure it to create a Lambda function and DynamoDB table.

### **Edit the** `serverless.yml` file as follows:

```yaml
# "org" ensures this Service is used with the correct Serverless Framework Access Key.
org: basir #change this according to your orgname
# "app" enables Serverless Framework Dashboard features and sharing them with other Services.
app: basirapp #change this to your app name
# "service" is the name of this project. This will also be added to your AWS resource names.
service: basirapp #change this according to your service name

stages:
  default:
    params:
      tableName: "basirtable" #change or create this on your aws account

provider:
  name: aws
  runtime: nodejs20.x
  region: ap-south-1
  iam:
    role:
      statements:
        - Effect: Allow
          Action:
            - dynamodb:Query
            - dynamodb:Scan
            - dynamodb:GetItem
            - dynamodb:PutItem
            - dynamodb:UpdateItem
            - dynamodb:DeleteItem
          Resource:
            - Fn::GetAtt: [UsersTable, Arn]
  environment:
    USERS_TABLE: ${param:tableName}
  httpApi:
    cors:
      allowedOrigins:
        - '*'  # Allows all origins; use specific origins for production
      allowedHeaders:
        - Content-Type
        - Authorization
        - X-Amz-Date
        - X-Api-Key
        - X-Amz-Security-Token
        - X-Requested-With
      allowedMethods:
        - GET
        - POST
        - PUT
        - DELETE
        - OPTIONS
      maxAge: 86400

functions:
  api:
    handler: handler.handler
    events:
      - httpApi:
          path: "/{proxy+}"  # Correct syntax for a catch-all route
          method: ANY

resources:
  Resources:
    UsersTable:
      Type: AWS::DynamoDB::Table
      Properties:
        AttributeDefinitions:
          - AttributeName: email
            AttributeType: S
        KeySchema:
          - AttributeName: email
            KeyType: HASH
        BillingMode: PAY_PER_REQUEST
        TableName: ${param:tableName}
```

### **Explanation:**

* 1. **org, app, and service**:
        
        * The `org` specifies the Serverless Framework organization (`basir`), ensuring that the service is linked with the correct Serverless Framework Access Key.
            
        * The `app` name (`basirapp`) enables features on the Serverless Framework Dashboard, facilitating easier management and sharing with other services.
            
        * The `service` (`basirapp`) is the name of this project, which will also be included in the names of the AWS resources created by this service.
            
    2. **stages**:
        
        * Defines a default stage with a parameter `tableName`, set to `"basirtable"`. This parameter is referenced throughout the configuration to specify the DynamoDB table name.
            
    3. **provider**:
        
        * Specifies AWS as the cloud provider and sets the runtime to `nodejs20.x`.
            
        * The `region` is defined as `ap-south-1`.
            
        * Configures an IAM role with permissions to perform various DynamoDB actions (`Query`, `Scan`, `GetItem`, `PutItem`, `UpdateItem`, and `DeleteItem`) on the specified DynamoDB table.
            
        * The `environment` section defines an environment variable `USERS_TABLE`, which utilizes the `tableName` parameter value (`basirtable`) for use within Lambda functions.
            
    4. **httpApi**:
        
        * Configures CORS (Cross-Origin Resource Sharing) settings to allow requests from any origin (`allowedOrigins: '*'`). For production environments, it is advisable to specify allowed origins explicitly.
            
        * Specifies the headers and HTTP methods that are permitted in requests, along with a cache duration of `maxAge: 86400` seconds (24 hours).
            
    5. **functions**:
        
        * Defines a Lambda function named `api`, with the handler implemented in `handler.handler`.
            
        * Sets up an `httpApi` event with a catch-all route (`/{proxy+}`) that allows all HTTP methods (`method: ANY`) to facilitate flexible request handling.
            
    6. **resources**:
        
        * Creates an AWS DynamoDB table named `UsersTable`.
            
        * The table's primary key is defined by the `email` attribute, which is of type `String` (`S`).
            
        * Configured with `BillingMode: PAY_PER_REQUEST`, allowing the table to automatically scale and bill based on the number of requests.
            
        * The table name is dynamically set using the `tableName` parameter (`basirtable`).
            

---

## **5\. Writing the Lambda Handler Code**

Next, create the Lambda function to save data to DynamoDB.

### **Steps:**

1. **Update handler.js code into your project**  
    In the root of the project. update `handler.js`.
    
2. **Add the Following Code**:
    
    ```javascript
    const { DynamoDBClient } = require("@aws-sdk/client-dynamodb");
    
    const {
      DynamoDBDocumentClient,
      GetCommand,
      PutCommand,
    } = require("@aws-sdk/lib-dynamodb");
    const crypto  = require("crypto-js");
    const jwt = require("jsonwebtoken");
    const express = require("express");
    const serverless = require("serverless-http");
    const cors = require('cors');
    const app = express();
    
    const USERS_TABLE = process.env.USERS_TABLE;
    const AES_SECRET = "56snbwuy#kdhuyethj39738626rhhgfd";
    const jwtSecret = "jskhshs54w57qjhyt2652geftsrhvhagskn@medgus";
    const client = new DynamoDBClient();
    const docClient = DynamoDBDocumentClient.from(client);
    
    app.use(express.json());
    const corsOptions = {
      origin: '*', // or '*' for all origins during development
      methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
      allowedHeaders: ['Content-Type', 'Authorization', 'X-Amz-Date', 'X-Api-Key', 'X-Amz-Security-Token', 'X-Requested-With'],
      credentials: true,
      optionsSuccessStatus: 200 // Some legacy browsers choke on status 204
    };
    
    // Use CORS with the specified options
    app.use(cors(corsOptions));
    app.get("/",(req,res)=>{
      res.send("Hello World")
    })
    //all routes starts from here
    app.post("/register", async (req, res) => {
      const {name,email,password,clg,phone} = req.body
      const hashpass = crypto.AES.encrypt(password,AES_SECRET).toString();
      //checking the user is exist or not;
      const getParams = {
        TableName: USERS_TABLE,
        Key: {
          email: email,
        },
      };
      try{
        const data = await docClient.send(new GetCommand(getParams));
      if(data.Item){
        return res.status(400).json({error:"User already exists",success:false})
      }
    }
      catch(err){
        console.log(err)
        res.status(500).json({error:"Could not create user",success:false})
      }
      //create user
      const params = {
        TableName: USERS_TABLE,
        Item: {
          name: name,
          email: email,
          password: hashpass,
          clg: clg,
          phone: phone,
        },
      };
    try{
    let a = await docClient.send(new PutCommand(params));
    res.status(200).json({message:"User created successfully",success:true})
    }
    catch(err){
      console.log(err)
      res.status(500).json({error:"Could not create user",success:false})
    }
    })
    //login endpoint
    app.post("/login", async (req, res) => {
      try{
        const {email,password} = req.body;
        const params = {
          TableName: USERS_TABLE,
          Key: {
            email: email,
          },
        };
        try{
         let data = await docClient.send(new GetCommand(params));
          if(!data.Item){
            return res.status(404).json({error:"User not found",success:false})
          }
          else{
            const decryptpass = crypto.AES.decrypt(data.Item.password,AES_SECRET).toString(crypto.enc.Utf8);
            if(decryptpass == password){
              const token = jwt.sign({email:email},jwtSecret);
              return res.status(200).json({message:"Login Successfull",success:true,token:token})
            }
            else{
              return res.status(401).json({error:"Invalid Password",success:false})
            }
          }
        }
        catch(err){
          console.log(err)
          res.status(500).json({error:"Login Failed ! Db Error.",success:false})
        }
      }
      catch(err){
        console.log(err)
        res.status(500).json({error:"Some thing Went Wrong .Try again later!",success:false})
      }
    })
    
    app.use((req, res, next) => {
      return res.status(404).json({
        error: "Not Found",
      });
    });
    exports.handler = serverless(app);
    ```
    

### **Explanation**:

* This Lambda function, implemented with Node.js and Express, interacts with Amazon DynamoDB to manage user registrations and logins. Key features include:
    
    1. **Dependencies**: Utilizes `@aws-sdk/client-dynamodb` for database operations, `crypto-js` for password encryption, `jsonwebtoken` for creating JWT tokens, and `express` for routing.
        
    2. **Environment Variables**: Configures `USERS_TABLE` for DynamoDB, along with secret keys for AES encryption and JWT signing.
        
    3. **Endpoints**:
        
        * **GET** `/`: Returns "Hello World" to indicate the server is running.
            
        * **POST** `/register`: Handles user registration by checking for existing users, encrypting passwords, and saving user data to DynamoDB.
            
        * **POST** `/login`: Authenticates users by verifying credentials and returning a JWT token upon successful login.
            
    4. **Error Handling**: Includes robust error handling to provide informative responses for various scenarios, such as user existence and authentication failures.
        
    5. **404 Handling**: Returns a 404 status for any undefined routes.
        
    6. **AWS Lambda Integration**: The `exports.handler` allows the Express app to run in the AWS Lambda environment.
        
    
    In summary, this function provides a secure user management system with encrypted passwords and JWT-based authentication, leveraging DynamoDB for data storage.
    

---

## **6\. Deploying the Application**

With everything configured, let’s deploy your application to AWS Lambda!

### **Steps:**

1. **Configure AWS Credentials**  
    Set up your AWS credentials on your local machine:
    
    ```bash
    serverless config credentials --provider aws --key YOUR_AWS_ACCESS_KEY --secret YOUR_AWS_SECRET_KEY
    ```
    
2. **Deploy the Application**  
    Run the following command to deploy your application:
    
    ```bash
    serverless deploy
    ```
    
    This command packages your application, uploads it to AWS, and creates the resources specified in `serverless.yml`.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730576361615/49458fa1-050d-4bf9-b5b3-9b96210ecdf2.png align="center")
    
    A DynamoDB table named "basirtable" is created through the `serverless.yml` configuration file, which defines the primary key as the `email` attribute. The table operates in `PAY_PER_REQUEST` billing mode, ensuring efficient scaling and cost management based on actual usage.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730576585614/88c9439f-63a0-4b9d-b404-56d30e1a786e.png align="center")
    
    Also check lamda our function is deployed successfully.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730576675721/f035b651-eb09-4bc5-b0b5-b383b7b440c8.png align="center")
    

---

## **7\. Testing the Deployment**

After deployment, test the endpoint to confirm that it works as expected.

1. **Get the Endpoint URL**  
    The deployment process will output an endpoint URL in the terminal.
    
2. **Send a Request to the URL**  
    Use a tool like curl or Postman to send a GET request to the endpoint:
    
    ```bash
    GET https://x60hefyye3.execute-api.ap-south-1.amazonaws.com
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730576808018/460dd8b5-62e8-49cb-862e-120c5ed920bd.png align="center")
    
    **Sends a post request to create an account to “/register” end point through postman.**
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730577066060/3f764f7d-747d-46ef-bbea-6232f92011b3.png align="center")
    
    Feel free to check another end point as well in my case all end point is working fine.
    

---

## **8\. Verifying DynamoDB Data**

Now, let’s confirm that the data was saved in DynamoDB.

1. **Go to the DynamoDB Console**  
    In your AWS Management Console, open DynamoDB.
    
2. **Check the Table**  
    Find your table (`MyTable`), and open it to view items. You should see the item created by your Lambda function.
    
3. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730577198201/3817e15c-f912-42bd-860c-b8a70920b743.png align="center")
    

---

## **9\. Wrapping Up**

Congratulations! 🎉 You’ve successfully deployed a Node.js app to AWS Lambda using the Serverless Framework and integrated it with DynamoDB. This setup demonstrates the power of serverless architecture, reducing costs and simplifying scaling while allowing you to quickly deploy applications.

By following this guide, you now have a foundational setup you can build upon, expanding your application’s features or integrating more AWS services. Enjoy building with serverless, and keep experimenting to leverage AWS Lambda and DynamoDB for even more advanced use cases!

---

## 10\. Bonous Section

In this section, we will connect the Lambda function to our frontend via a REST API, allowing seamless interaction between the backend and frontend components. This integration will enable us to handle requests efficiently, enhancing the application's responsiveness and user experience. Let's proceed with setting up the API endpoint to establish this connection.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">I will create a frontend for this integration. You can either clone the provided frontend repository or use your own—both approaches will follow similar steps. We’ll update the API endpoint accordingly to connect it with the Lambda function and ensure a smooth data flow between the frontend and backend. Let's proceed with setting up the frontend and linking it to the updated API endpoint.</div>
</div>

1\. Clone the github repo .

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">If you don’t have Git installed on your machine, feel free to download the repository as a ZIP file. Then, extract it, and all the remaining steps will be the same.</div>
</div>

```bash
git clone https://github.com/BasirKhan418/basirapp-serverless-frontend.git
```

```bash
cd basirapp-serverless-frontend
```

```bash
npm i
```

2. Create a `.env` file and add the following content. Feel free to update the value of `VITE_BACKEND_URL` with the URL of your own Lambda function deployment or API gateway:
    
    ```bash
    VITE_BACKEND_URL=<your_lambda_function_url>
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730579817763/c0e377e7-d931-4e74-b214-61424fb57695.png align="center")
    
3. Build your project / frontend.
    

```bash
npm run build
```

4. After a successful build, a `dist` folder will be generated. This folder contains the production-ready files, which we need to upload to S3 to host the frontend application.
    
5. Create an S3 bucket with your desired name. Uncheck the option to block all public access, leave all other settings as default, and click **Create bucket** to proceed.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730580363271/3cf6d713-64d4-4edf-badd-423c3f048a85.png align="center")
    
6. Click on recently created bucket go to permissions and click on edit bucket policy and paste the content below of it and make sure to add your s3 arn.
    
    ```bash
    {
    	"Version": "2012-10-17",
    	"Statement": [
    		{
    			"Sid": "Statement1",
    			"Principal": "*",
    			"Effect": "Allow",
    			"Action": [
    				"s3:GetObject"
    			],
    			"Resource": "your arn/*"
    		}
    	]
    }
    ```
    
7. Upload you dist folder contents to s3
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730580729210/c919730a-d09f-4d81-af7f-8ec6aaa9f768.png align="center")
    
8. Navigate to the **Properties** tab, scroll down to the **Static website hosting** section at the bottom of the page, and click **Edit**. Enable static website hosting, enter `index.html` in the **Index document** field, and click **Save changes** to apply.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730580954753/4bdd6a1d-8ee6-438d-afb4-1c47b39dd83e.png align="center")
    
9. 🎉 Hooray! Your frontend has been deployed successfully.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730581109381/e6a62c9f-14c8-4db5-926c-43641dc1ea5c.png align="center")
    
10. Open the generated link in a new tab, try registering, and check if the data is being saved in DynamoDB.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730581276766/b7e24a61-0b52-4b16-b1c9-f3ccd881ea9b.png align="center")
    

## **Conclusion:**

By following this guide, titled **"Deploy a Node.js App on AWS Lambda with DynamoDB Using Serverless Framework: Step-by-Step Guide,"** you've successfully deployed a full-stack, serverless registration and login system using AWS. Your Node.js backend now operates on AWS Lambda, with DynamoDB handling data storage, ensuring both security and scalability. Additionally, hosting the frontend on S3 offers a cost-effective, high-performance solution for web access.

This setup not only provides a robust foundation for authentication workflows but also streamlines application management by leveraging AWS services. With this approach, you're well-equipped to scale effortlessly, adding new features or integrating additional AWS services as needed.

In summary, this beginner-friendly, step-by-step guide has equipped you with the knowledge to deploy a fully functional serverless application in the cloud, perfect for developers looking to learn AWS Lambda and DynamoDB integration!
# awsLambda_springboot serverless API
The awsLambda_springboot project, created with [`aws-serverless-java-container`](https://github.com/aws/serverless-java-container).

The starter project defines a simple `/ping` resource that can accept `GET` requests with its tests.

The project folder also includes a `template.yml` file. You can use this [SAM](https://github.com/awslabs/serverless-application-model) file to deploy the project to AWS Lambda and Amazon API Gateway or test in local with the [SAM CLI](https://github.com/awslabs/aws-sam-cli). 

## Pre-requisites
* [AWS CLI](https://aws.amazon.com/cli/)
* [SAM CLI](https://github.com/awslabs/aws-sam-cli)
* [Gradle](https://gradle.org/) or [Maven](https://maven.apache.org/)

## Building the project
You can use the SAM CLI to quickly build the project
```bash
$ mvn archetype:generate -DartifactId=awsLambda_springboot -DarchetypeGroupId=com.amazonaws.serverless.archetypes -DarchetypeArtifactId=aws-serverless-jersey-archetype -DarchetypeVersion=2.1.2 -DgroupId=org.example -Dversion=1.0-SNAPSHOT -Dinteractive=false
$ cd awsLambda_springboot
$ sam build
Building resource 'AwsLambda_springbootFunction'
Running JavaGradleWorkflow:GradleBuild
Running JavaGradleWorkflow:CopyArtifacts

Build Succeeded

Built Artifacts  : .aws-sam/build
Built Template   : .aws-sam/build/template.yaml

Commands you can use next
=========================
[*] Invoke Function: sam local invoke
[*] Deploy: sam deploy --guided
```

## Testing locally with the SAM CLI

From the project root folder - where the `template.yml` file is located - start the API with the SAM CLI.

```bash
$ sam local start-api

...
Mounting com.amazonaws.serverless.archetypes.StreamLambdaHandler::handleRequest (java11) at http://127.0.0.1:3000/{proxy+} [OPTIONS GET HEAD POST PUT DELETE PATCH]
...
```

Using a new shell, you can send a test ping request to your API:

```bash
$ curl -s http://127.0.0.1:3000/ping | python -m json.tool

{
    "pong": "Hello, World!"
}
``` 

## Deploying to AWS
To deploy the application in your AWS account, you can use the SAM CLI's guided deployment process and follow the instructions on the screen

```
$ sam deploy --guided
```

Once the deployment is completed, the SAM CLI will print out the stack's outputs, including the new application URL. You can use `curl` or a web browser to make a call to the URL

```
...
-------------------------------------------------------------------------------------------------------------
OutputKey-Description                        OutputValue
-------------------------------------------------------------------------------------------------------------
AwsLambda_springbootApi - URL for application            https://xxxxxxxxxx.execute-api.us-west-2.amazonaws.com/Prod/pets
-------------------------------------------------------------------------------------------------------------
```

Copy the `OutputValue` into a browser or use curl to test your first request:

```bash
$ curl -s https://xxxxxxx.execute-api.us-west-2.amazonaws.com/Prod/ping | python -m json.tool

{
    "pong": "Hello, World!"
}
```
------------------
# Spring Boot on AWS Lambda

This project demonstrates how to deploy a Spring Boot application to AWS Lambda by using `aws-serverless-java-container-springboot3` and expose it through API Gateway.

---
## 🛠 Prerequisites

- Java 11 or later (this example uses Java 21)
- Maven or Gradle
- Lambda Execution Role with basic permissions
---

## 📦 Dependencies

Add the following to your `pom.xml`:

```xml
       <dependency>
            <groupId>com.amazonaws.serverless</groupId>
            <artifactId>aws-serverless-java-container-springboot3</artifactId>
            <version>2.1.2</version>
        </dependency>
```

---

## 💡 Lambda Handler Example

```java
public class StreamLambdaHandler implements RequestStreamHandler {
    private static SpringBootLambdaContainerHandler<AwsProxyRequest, AwsProxyResponse> handler;
    static {
        try {
            handler = SpringBootLambdaContainerHandler.getAwsProxyHandler(Application.class);
        } catch (ContainerInitializationException e) {
            // if we fail here. We re-throw the exception to force another cold start
            e.printStackTrace();
            throw new RuntimeException("Could not initialize Spring Boot application", e);
        }
    }

    @Override
    public void handleRequest(InputStream inputStream, OutputStream outputStream, Context context)
            throws IOException {
        handler.proxyStream(inputStream, outputStream, context);
    }
}
```
---

## 🚀 Deployment Process

1. **Build the JAR or zip**
   ```bash
   mvn clean package
   ```
![Screenshot 2025-04-15 123725](https://github.com/user-attachments/assets/2af75d81-9d18-4b40-a1b3-f1c93f6b40f0)

![Screenshot 2025-04-15 130040](https://github.com/user-attachments/assets/915f5e36-4c2f-404d-a5af-f393b8584e02)


2. **Create Lambda Function (AWS Console)**
   - In the AWS Console, create a function as per your project requirements.
   - For this project, it is named `DemoLambda` and uses Java 21.
   - Upload the ZIP file created from your project (`.zip` containing compiled JAR and dependencies).
   - Edit the runtime to Java 21 (or your target version).
   - Set the handler as per your package and class (e.g., `org.example.StreamLambdaHandler::handleRequest`).

3. **Test the API**
   - Use the built-in test functionality in the Lambda console.
![Screenshot 2025-04-13 234742](https://github.com/user-attachments/assets/81ddd099-5ea1-4b65-8630-68577318fbdc)


4. **Expose via API Gateway**
   - Create a REST API in API Gateway.
   - Use Lambda Proxy integration.
   - Deploy the API to a stage (e.g., `Demoapi`).
![Screenshot 2025-04-13 235112](https://github.com/user-attachments/assets/d410d1a3-3d0f-4876-9856-4af57ea0be41)

---

## 🔗 Live API Endpoint

The deployed API is accessible at:

👉 [`https://l0lde21y33.execute-api.us-east-1.amazonaws.com/Demoapi/ping`](https://l0lde21y33.execute-api.us-east-1.amazonaws.com/Demoapi/ping)
!



Test it with:

```bash
curl https://l0lde21y33.execute-api.us-east-1.amazonaws.com/Demoapi/ping
```
![Screenshot 2025-04-14 001425](https://github.com/user-attachments/assets/830a0ea8-dd4b-4ba9-a3a4-9f3466994273)

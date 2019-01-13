# AWSWorkshop-20190109-CDK-CFN-SERVERLESS
This workshop is intended to demonstrate the use of AWS CloudDevelopmentKit, hereby referred to as AWS CDK. Using AWS CDK we will go through step by step how to create a serverless web application from scratch. 

#### What is AWS CDK?
*Quoted directly from the open-source [repo](https://github.com/awslabs/aws-cdk)*: The AWS Cloud Development Kit (AWS CDK) is an open-source software development framework to define cloud infrastructure in code and provision it through AWS CloudFormation. The CDK integrates fully with AWS services and offers a higher level object-oriented abstraction to define AWS resources imperatively. Using the CDK’s library of infrastructure constructs, you can easily encapsulate AWS best practices in your infrastructure definition and share it without worrying about boilerplate logic. The CDK improves the end-to-end development experience because you get to use the power of modern programming languages to define your AWS infrastructure in a predictable and efficient manner.

#### Why should I use AWS CDK, or infrastructure as code?!?!
One of major reasons for unforseen outages and customer impacting issues is due to lack of change management.

#### Pre-reqs
1. A **personal** AWS Account

## Step 0 - Setup a clean development environment
Since everyone has their own preconfigured laptops with their own customized development environments, things could get pretty messy. So, lets use AWS Cloud9 for the sake of this lab, AWS Cloud9 provides a hosted IDE to write, run and debug code. It also comes with awscli preinstalled!

1. In the console, the Services button in the top left will reveal a dropdown with all the services.
2. Find or search for **Cloud9**.
3. In the middle right click **Create Environment**.
4. Give the Cloud9 environment a name (for example *cdk-cfn-demo-env*) and leave everything else as standard, create your environment.
5. In the bottom there is a terminal, lets install and initalize the CDK
    ```
    nvm install --lts
    nvm update npm
    npm install -g aws-cdk
    cdk --version

    cd cdk-cfn-demo-env
    cdk init --language typescript
    ```
    
## Step 1 - Start building our cdk template
1. Make our main App file
    ```
    mkdir resources
    touch widgets.js
    ```
2. Open the widgets.js file and paste the following:
    ```
    const AWS = require('aws-sdk');
    const S3 = new AWS.S3();

    const bucketName = process.env.BUCKET;

    exports.main = async function(event, context) {
      try {
        var method = event.httpMethod;

        if (method === "GET") {
          if (event.path === "/") {
            const data = await S3.listObjectsV2({ Bucket: bucketName }).promise();
            var body = {
              widgets: data.Contents.map(function(e) { return e.Key })
            };
            return {
              statusCode: 200,
              headers: {},
              body: JSON.stringify(body)
            };
          }
        }

        // We only accept GET for now
        return {
          statusCode: 400,
          headers: {},
          body: "We only accept GET /"
        };
      } catch(error) {
        var body = error.stack || JSON.stringify(error, null, 2);
        return {
          statusCode: 400,
            headers: {},
            body: JSON.stringify(body)
        }
      }
    }
    ```
    After done pasting run the following:
    ```
    npm run build
    cdk synth
    ```
3. Install ApiGateway
    ```
    npm i @aws-cdk/aws-apigateway@0.22.0
    ```
    
4. Install S3
    ```
    npm install @aws-cdk/aws-s3
    cdk synth --app index.js > ./cfn.yml
    ```



# Run AWS Lambda functions<a name="run-lambda-functions"></a>

You can import AWS Lambda functions as components that run on AWS IoT Greengrass core devices\. You might want to do this in the following cases:
+ You have application code in Lambda functions that you want to deploy to core devices\.
+ You have AWS IoT Greengrass V1 applications that you want to run on AWS IoT Greengrass V2 core devices\. For more information, see [Run AWS IoT Greengrass V1 applications on AWS IoT Greengrass V2](move-from-v1.md#run-v1-applications)\.

Lambda functions include dependencies on the following components\. You don't need to define these components as dependencies when you import the function\. When you deploy the Lambda function component, the deployment includes these Lambda component dependencies\.
+ The [Lambda launcher component](lambda-launcher-component.md) \(`aws.greengrass.LambdaLauncher`\) handles processes and environment configuration\.
+ The [Lambda manager component](lambda-manager-component.md) \(`aws.greengrass.LambdaManager`\) handles interprocess communication and scaling\.
+ The [Lambda runtimes component](lambda-runtimes-component.md) \(`aws.greengrass.LambdaRuntimes`\) provides artifacts for each supported Lambda runtime\.

**Topics**
+ [Requirements](#run-lambda-functions-requirements)
+ [Import a Lambda function as a component \(console\)](import-lambda-function-console.md)
+ [Import a Lambda function as a component \(AWS CLI\)](import-lambda-function-cli.md)

## Requirements<a name="run-lambda-functions-requirements"></a>

Your core devices and Lambda functions must meet the following requirements for you to run the functions on the AWS IoT Greengrass Core software:
+ You must install the programming languages that the Lambda function uses on your core devices\.
**Tip**  
You can create a component that installs the programming language, and then specify that component as a dependency of your Lambda function component\.
+ Your Lambda function must use one of the following runtimes:
  + Python 2\.7 – `python2.7`
  + Python 3\.7 – `python3.7`
  + Python 3\.8 – `python3.8`
  + Java 8 – `java8`
  + Node\.js 10 – `nodejs10.x`
  + Node\.js 12 – `nodejs12.x`
+ <a name="core-device-lambda-function-requirements"></a>Your core device must meet the requirements to run Lambda functions\. If you want the core device to run containerized Lambda functions, the device must meet the requirements to do so\. For more information, see [Requirements to run Lambda functions](setting-up.md#greengrass-v2-lambda-requirements)\.
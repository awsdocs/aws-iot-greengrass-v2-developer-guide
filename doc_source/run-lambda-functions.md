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
+ [Configure Lambda function lifecycle](#lambda-lifecycle)
+ [Configure Lambda function containerization](#lambda-containerization)
+ [Import a Lambda function as a component \(console\)](import-lambda-function-console.md)
+ [Import a Lambda function as a component \(AWS CLI\)](import-lambda-function-cli.md)

## Requirements<a name="run-lambda-functions-requirements"></a>

Your core devices and Lambda functions must meet the following requirements for you to run the functions on the AWS IoT Greengrass Core software:
+ <a name="core-device-lambda-function-requirements"></a>Your core device must meet the requirements to run Lambda functions\. If you want the core device to run containerized Lambda functions, the device must meet the requirements to do so\. For more information, see [Requirements to run Lambda functions](setting-up.md#greengrass-v2-lambda-requirements)\.
+ You must install the programming languages that the Lambda function uses on your core devices\.
**Tip**  
You can create a component that installs the programming language, and then specify that component as a dependency of your Lambda function component\.
+ Your Lambda function must use one of the following runtimes:
  + Python 3\.8 – `python3.8`
  + Python 3\.7 – `python3.7`
  + Python 2\.7 – `python2.7` \*
  + Java 8 – `java8`
  + Node\.js 12 – `nodejs12.x`
  + Node\.js 10 – `nodejs10.x`

  \* You can run Lambda functions that use this runtime on AWS IoT Greengrass, but you can't create them in AWS Lambda\. For more information, see [Runtime support policy](https://docs.aws.amazon.com/lambda/latest/dg/runtime-support-policy.html) in the *AWS Lambda Developer Guide*\.

## Configure Lambda function lifecycle<a name="lambda-lifecycle"></a>

The Greengrass Lambda function lifecycle determines when a function starts and how it creates and uses containers\. The lifecycle also determines how the AWS IoT Greengrass Core software retains variables and preprocessing logic that are outside of the function handler\.

AWS IoT Greengrass supports on\-demand \(default\) and long\-lived lifecycles:
+ **On\-demand** functions start when they are invoked and stop when there are no tasks left to run\. Each invocation of the function creates a separate container, also called a sandbox, to process invocations, unless an existing container is available for reuse\. Any of the containers might process data that you send to the function\.

  Multiple invocations of an on\-demand function can run simultaneously\.

  Variables and preprocessing logic that you define outside of the function handler are not retained when new containers are created\.
+ **Long\-lived** \(or *pinned*\) functions start when the AWS IoT Greengrass Core software starts and run in a single container\. The same container processes all data that you send to the function\.

  Multiple invocations are queued until the AWS IoT Greengrass Core software runs earlier invocations\.

  Variables and preprocessing logic that you define outside of the function handler are retained for every invocation of the handler\.

  Use long\-lived Lambda functions when you need to start doing work without any initial input\. For example, a long\-lived function can load and start processing a machine learning model to be ready when the function receives device data\.
**Note**  
Long\-lived functions have timeouts that are associated with each invocation of their handler\. If you want to invoke code that runs indefinitely, you must start it outside of the handler\. Make sure that there's no blocking code outside of the handler that might prevent the function from initializing\.  
These functions run unless the AWS IoT Greengrass Core software stops, such as during a deployment or reboot\. These functions won't run if the function encounters an uncaught exception, exceeds its memory limits, or enters an error state, such as a handler timeout\.

For more information about container reuse, see [Understanding Container Reuse in AWS Lambda](http://aws.amazon.com/blogs/compute/container-reuse-in-lambda/) in the *AWS Compute Blog*\.

## Configure Lambda function containerization<a name="lambda-containerization"></a>

By default, Lambda functions run inside of an AWS IoT Greengrass container\. Greengrass containers provide isolation between your functions and the host\. This isolation increases security for both the host and the functions in the container\.

We recommend that you run Lambda functions in a Greengrass container, unless your use case requires them to run without containerization\. By running your Lambda functions in a Greengrass container, you have more control over how you restrict access to resources\.

You might run a Lambda function without containerization in the following cases:
+ You want to run AWS IoT Greengrass on a device that doesn't support container mode\. An example would be if you wanted to use a special Linux distribution, or have an earlier kernel version that is out of date\.
+ You want to run your Lambda function in another container environment with its own OverlayFS, but encounter OverlayFS conflicts when you run in a Greengrass container\.
+ You need access to local resources with paths that can't be determined at deployment time, or whose paths can change after deployment\. An example of this resource would be a pluggable device\.
+ You have an earlier application that was written as a process, and you encounter issues when you run it in a Greengrass container\.


**Containerization differences**  

| Containerization | Notes | 
| --- | --- | 
|  Greengrass container  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/run-lambda-functions.html)  | 
|  No container  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/run-lambda-functions.html)  | 

If you change the containerization for a Lambda function when you deploy it, the function might not work as expected\. If the Lambda function uses local resources that are no longer available with the new containerization setting, deployment fails\.
+ When you change a Lambda function from running in a Greengrass container to running without containerization, the function's memory limits are discarded\. You must access the file system directly instead of using attached local resources\. You must remove any attached resources before you deploy the Lambda function\.
+ When you change a Lambda function from running without containerization to running in a container, your Lambda function loses direct access to the file system\. You must define a memory limit for each function or accept the default 16 MB memory limit\. You can configure these settings for each Lambda function when you deploy it\.

To change containerization settings for a Lambda function component, set the value of the `containerMode` configuration parameter to one of the following options when you deploy the component\.<a name="lambda-function-component-container-mode-parameter"></a>
+ `NoContainer` – The component doesn't run in an isolated runtime environment\.
+ `GreengrassContainer` – The component runs in an isolated runtime environment inside the AWS IoT Greengrass container\.

For more information about how to deploy and configure components, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md) and [Update component configurations](update-component-configurations.md)\.
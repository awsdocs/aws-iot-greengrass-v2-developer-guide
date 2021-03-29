# Lambda manager<a name="lambda-manager-component"></a>

The Lambda manager component \(`aws.greengrass.LambdaManager`\) manages work items and interprocess communication for AWS Lambda functions that run on the Greengrass core device\.

**Note**  <a name="lambda-component-dependency-note"></a>
When you deploy a Lambda function component to a core device, the deployment also includes this component\. For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\.

This component has the following versions:
+ 2\.0\.x

## Requirements<a name="lambda-manager-component-requirements"></a>

This component has the following requirements:
+ <a name="core-device-lambda-function-requirements"></a>Your core device must meet the requirements to run Lambda functions\. If you want the core device to run containerized Lambda functions, the device must meet the requirements to do so\. For more information, see [Requirements to run Lambda functions](setting-up.md#greengrass-v2-lambda-requirements)\.

## Configuration<a name="lambda-manager-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`getResultTimeoutInSecond`  
\(Optional\) The maximum amount of time in seconds that Lambda functions can run before they time out\.  
Default: `60`
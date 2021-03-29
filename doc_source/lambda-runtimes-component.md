# Lambda runtimes<a name="lambda-runtimes-component"></a>

The Lambda runtimes component \(`aws.greengrass.LambdaRuntimes`\) provides the runtimes that Greengrass core devices use to run AWS Lambda functions\.

**Note**  <a name="lambda-component-dependency-note"></a>
When you deploy a Lambda function component to a core device, the deployment also includes this component\. For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\.

This component has the following versions:
+ 2\.0\.x

## Requirements<a name="lambda-runtimes-component-requirements"></a>

This component has the following requirements:
+ <a name="core-device-lambda-function-requirements"></a>Your core device must meet the requirements to run Lambda functions\. If you want the core device to run containerized Lambda functions, the device must meet the requirements to do so\. For more information, see [Requirements to run Lambda functions](setting-up.md#greengrass-v2-lambda-requirements)\.
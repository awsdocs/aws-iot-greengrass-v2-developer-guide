# Greengrass feature compatibility by operating system<a name="operating-system-feature-support-matrix"></a>

AWS IoT Greengrass supports devices that run various operating systems\. Some features are supported on only certain operating systems\. Use the following tables to learn which features are available for each supported operating system\. For more information about supported operating systems, requirements, and how to set up Greengrass core devices, see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.


**Messaging**  

| Feature | Linux | Windows | 
| --- | --- | --- | 
|  Exchange MQTT messages between AWS IoT and components  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Exchange local publish/subscribe messages between components  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Interact with local IoT devices over MQTT  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Interact with local Modbus\-RTU devices using the Modbus\-RTU component  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 


**Security**  

| Feature | Linux | Windows | 
| --- | --- | --- | 
|  Secure connections with device authentication and authorization  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Deploy and access secure, encrypted secrets from AWS Secrets Manager  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Use a hardware security module \(HSM\) to securely store the device's private key and certificate  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Audit core devices with AWS IoT Device Defender  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Use AWS credentials to interact with AWS services  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 


**Installation**  

| Feature | Linux | Windows | 
| --- | --- | --- | 
|  Install AWS IoT Greengrass with automatic provisioning  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Install AWS IoT Greengrass with manual provisioning  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Install AWS IoT Greengrass with AWS IoT fleet provisioning  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Install AWS IoT Greengrass with custom provisioning plugins  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Run AWS IoT Greengrass in a Docker container using a prebuilt Docker image  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 


**Remote maintenance and updates**  

| Feature | Linux | Windows | 
| --- | --- | --- | 
|  Perform secure, over\-the\-air \(OTA\) software updates  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Manage core devices with AWS Systems Manager  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Connect to core devices with AWS IoT secure tunneling  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 


**Machine learning**  

| Feature | Linux | Windows | 
| --- | --- | --- | 
|  Perform machine learning inference using Amazon SageMaker Edge Manager  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Perform machine learning inference using Amazon Lookout for Vision  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Perform machine learning inference using DLR  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Perform machine learning inference using TensorFlow  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 


**Component features**  

| Feature | Linux | Windows | 
| --- | --- | --- | 
|  Deploy and invoke Lambda functions  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Run Docker containers in components  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Process and export high\-volume data streams using stream manager  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Manage component lifecycles with lifecycle scripts  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Interact with device shadows  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Upload logs to Amazon CloudWatch Logs  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Upload data to Amazon CloudWatch metrics using the CloudWatch metrics component  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Publish messages to Amazon Simple Notification Service using the Amazon SNS component  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Publish data to Amazon Kinesis Data Firehose delivery streams using stream manager  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Publish data to Amazon Kinesis Data Firehose delivery streams using the Kinesis Data Firehose component  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Gather and act on real\-time system telemetry metrics  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Configure system resource limits for component processes  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Pause and resume component processes  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Integrate with AWS IoT SiteWise using the AWS IoT SiteWise components  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Publish video streams to Amazon Kinesis Video Streams using the edge connector for Kinesis Video Streams component  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 


**Component development**  

| Feature | Linux | Windows | 
| --- | --- | --- | 
|  Develop components locally on core devices  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Interact with a core device using the AWS IoT Greengrass CLI  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Interact with a core device using the local debug console  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Use the AWS IoT Device SDK for Python in custom components  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Use the AWS IoT Device SDK for C\+\+ in custom components  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Use the AWS IoT Device SDK for Java in custom components  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 


**Device certification**  

| Feature | Linux | Windows | 
| --- | --- | --- | 
|  Use AWS IoT Device Tester for AWS IoT Greengrass V2 to validate IoT devices  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
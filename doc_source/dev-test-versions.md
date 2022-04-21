# Supported versions of AWS IoT Device Tester for AWS IoT Greengrass V2<a name="dev-test-versions"></a>

This topic lists supported versions of IDT for AWS IoT Greengrass V2\. As a best practice, we recommend that you use the latest version of IDT for AWS IoT Greengrass V2 that supports your target version of AWS IoT Greengrass V2\. New releases of AWS IoT Greengrass might require you to download a new version of IDT for AWS IoT Greengrass V2\. You receive a notification when you start a test run if IDT for AWS IoT Greengrass V2 is not compatible with the version of AWS IoT Greengrass you are using\.

By downloading the software, you agree to the [AWS IoT Device Tester License Agreement](https://docs.aws.amazon.com/greengrass/v2/developerguide/idt-license.html)\.

**Note**  
<a name="unzip-package-to-local-drive"></a>IDT does not support being run by multiple users from a shared location, such as an NFS directory or a Windows network shared folder\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.

## Latest IDT version for AWS IoT Greengrass V2<a name="idt-latest-version"></a>

You can use this version of IDT for AWS IoT Greengrass V2 with the AWS IoT Greengrass version listed here\. <a name="idt-latest-version.options"></a>

**IDT v4\.5\.3 for AWS IoT Greengrass**    
Supported AWS IoT Greengrass versions:   
+ [Greengrass nucleus](greengrass-nucleus-component.md) v2\.5\.5  
IDT software downloads:  
+ IDT v4\.5\.3 with test suite GGV2Q\_2\.3\.1 for [Linux](https://docs.aws.amazon.com/greengrass/v2/developerguide/devicetester_greengrass_v2_4.5.3_testsuite_2.3.1_linux.zip)
+ IDT v4\.5\.3 with test suite GGV2Q\_2\.3\.1 for [macOS](https://docs.aws.amazon.com/greengrass/v2/developerguide/devicetester_greengrass_v2_4.5.3_testsuite_2.3.1_mac.zip)
+ IDT v4\.5\.3 with test suite GGV2Q\_2\.3\.1 for [Windows](https://docs.aws.amazon.com/greengrass/v2/developerguide/devicetester_greengrass_v2_4.5.3_testsuite_2.3.1_win.zip)  
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.5\.5\.
+ Updates DockerApplicationManager test to use ECR\-based docker image\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_2.3.1`  
+ Released 2022\.04\.15

## Unsupported versions of AWS IoT Device Tester for AWS IoT Greengrass V2<a name="idt-unsupported-versions"></a>

This topic lists unsupported versions of IDT for AWS IoT Greengrass V2\. Unsupported versions do not receive bug fixes or updates\. For more information, see [Support policy for AWS IoT Device Tester for AWS IoT Greengrass](idt-support-policy.md)\.

**IDT v4\.5\.1 for AWS IoT Greengrass**    
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.5\.3\.
+ Adds support for validating and qualifying Linux\-based devices that use a hardware security module \(HSM\) to store the private key and certificate that are used by AWS IoT Greengrass Core software\. 

  Implements the new IDT test orchestrator for configuring custom test suites\. For more information, see [Configure the IDT test orchestrator](idt-test-orchestrator.md)\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_2.3.0`  
+ Released 2022\.01\.11

**IDT v4\.4\.1 for AWS IoT Greengrass**    
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.5\.2\.
+ Adds support for using a user\-defined IAM role as the token exchange role that the device under test assumes to interact with AWS resources\. 

  You can specify the IAM role in the [`userdata.json` file](set-config.md#userdata-config)\. If you specify a custom role, IDT uses that role instead of creating the default token exchange role during the test run\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_2.2.1`  
+ Released 2021\.12\.12

**IDT v4\.4\.0 for AWS IoT Greengrass**    
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.5\.0\.
+ Adds support for validating and qualifying devices running AWS IoT Greengrass Core software on Windows\.
+ Supports the use of public key validation for secure shell \(SSH\) device connections\.
+ Improves the IDT permissions IAM policy with security best practices\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_2.1.0`  
+ Released 2021\.11\.19

**IDT v4\.2\.0 for AWS IoT Greengrass**    
Release notes:  
+ Includes support for qualification of the following features on devices running AWS IoT Greengrass Core software v2\.2\.0 and later versions: 
  + Docker—Validates that devices can download a Docker container image from Amazon Elastic Container Registry \(Amazon ECR\)\.
  + Machine learning—Validates that devices can perform machine learning \(ML\) inference using the [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) or [TensorFlow Lite](https://www.tensorflow.org/lite/guide/python) ML frameworks\.
  + Stream manager—Validates that devices can download, install, and run the AWS IoT Greengrass stream manager\.
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.4\.0, v2\.3\.0, v2\.2\.0, and v2\.1\.0\.
+ Groups the test logs for each test case in a separate *<test\-case\-id>* folder within the `<device-tester-extract-location>/results/<execution-id>/logs/<test-group-id>` directory\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_2.0.1`  
+ Released 2021\.08\.31

**IDT v4\.1\.0 for AWS IoT Greengrass**    
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.3\.0, v2\.2\.0, v2\.1\.0, and v2\.0\.5\.
+ Improves the `userdata.json` configuration by removing the requirement to specify the `GreengrassNucleusVersion` and `GreengrassCLIVersion` properties\. 
+ Includes support for Lambda and MQTT feature qualification for AWS IoT Greengrass Core software v2\.1\.0 and later versions\. You can now use IDT for AWS IoT Greengrass V2 to validate that your core device can run Lambda functions and that the device can publish and subscribe to AWS IoT Core MQTT topics\.
+ Improves logging capabilities\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_1.1.1`  
+ Released 2021\.06\.18

**IDT v4\.0\.2 for AWS IoT Greengrass**    
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.1\.0\.
+ Adds support for Lambda and MQTT feature qualification for AWS IoT Greengrass Core software v2\.1\.0 and later versions\. You can now use IDT for AWS IoT Greengrass V2 to validate that your core device can run Lambda functions and that the device can publish and subscribe to AWS IoT Core MQTT topics\.
+ Improves logging capabilities\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_1.1.1`  
+ Released 2021\.05\.05

**IDT v4\.0\.1 for AWS IoT Greengrass**    
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Version 2 software\.
+ Enables you to develop and run your custom test suites using AWS IoT Device Tester for AWS IoT Greengrass\. For more information, see [Use IDT to develop and run your own test suites](idt-custom-tests.md)\.
+ Provides code signed IDT applications for macOS and Windows\. On macOS, you might need to grant a security exception for IDT\. For more information, see [Security exception on macOS](idt-troubleshooting.md#security-exception-macos)\.  
Test suite version:    
`GGV2Q_1.0.0`  
+ Released 2020\.12\.22
+ The test suite runs only required tests for qualification, unless you set the corresponding `value` in the `features` array to `yes`\.
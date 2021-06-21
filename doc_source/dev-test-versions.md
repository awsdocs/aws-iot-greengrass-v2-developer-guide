# Supported versions of AWS IoT Device Tester for AWS IoT Greengrass V2<a name="dev-test-versions"></a>

This topic lists supported versions of IDT for AWS IoT Greengrass V2\. As a best practice, we recommend that you use the latest version of IDT for AWS IoT Greengrass V2 that supports your target version of AWS IoT Greengrass V2\. New releases of AWS IoT Greengrass might require you to download a new version of IDT for AWS IoT Greengrass\. You receive a notification when you start a test run if IDT for AWS IoT Greengrass is not compatible with the version of AWS IoT Greengrass you are using\.

By downloading the software, you agree to the [AWS IoT Device Tester License Agreement](https://docs.aws.amazon.com/greengrass/v2/developerguide/idt-license.html)\.

**Note**  
<a name="unzip-package-to-local-drive"></a>IDT does not support being run by multiple users from a shared location, such as an NFS directory or a Windows network shared folder\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.

## Latest IDT version for AWS IoT Greengrass<a name="idt-latest-version"></a>

You can use this version of IDT for AWS IoT Greengrass with the AWS IoT Greengrass version listed here\. 

**IDT v4\.1\.0 for AWS IoT Greengrass**    
Supported AWS IoT Greengrass component versions:   
+ [Greengrass nucleus](greengrass-nucleus-component.md) v2\.2\.0, v2\.1\.0, and v2\.0\.5  
IDT software downloads:  
+ IDT v4\.1\.0 with test suite GGV2Q\_1\.1\.1 for [Linux](https://docs.aws.amazon.com/greengrass/v2/developerguide/download-idt-gg2-4.1.0-testsuite-1.1.1-linux.html)
+ IDT v4\.1\.0 with test suite GGV2Q\_1\.1\.1 for [macOS](https://docs.aws.amazon.com/greengrass/v2/developerguide/download-idt-gg2-4.1.0-testsuite-1.1.1-mac.html)
+ IDT v4\.1\.0 with test suite GGV2Q\_1\.1\.1 for [Windows](https://docs.aws.amazon.com/greengrass/v2/developerguide/download-idt-gg2-4.1.0-testsuite-1.1.1-win.html)  
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.2\.0, v2\.1\.0, and v2\.0\.5\.
+ Improves the `userdata.json` configuration by removing the requirement to specify the `GreengrassNucleusVersion` and `GreengrassCLIVersion` properties\. 
+ Includes support for Lambda and MQTT feature qualification for AWS IoT Greengrass Core software v2\.1\.0 and later versions\. You can now use IDT for AWS IoT Greengrass to validate that your core device can run Lambda functions and that the device can publish and subscribe to AWS IoT Core MQTT topics\.
+ Improves logging capabilities\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_1.1.0`  
+ Released 2021\.06\.18

## Unsupported versions of AWS IoT Device Tester for AWS IoT Greengrass<a name="idt-unsupported-versions"></a>

This topic lists unsupported versions of IDT for AWS IoT Greengrass\. Unsupported versions do not receive bug fixes or updates\. For more information, see [Support policy for AWS IoT Device Tester for AWS IoT Greengrass](idt-support-policy.md)\.

**IDT v4\.0\.2 for AWS IoT Greengrass**    
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.1\.0\.
+ Adds support for Lambda and MQTT feature qualification for AWS IoT Greengrass Core software v2\.1\.0 and later versions\. You can now use IDT for AWS IoT Greengrass to validate that your core device can run Lambda functions and that the device can publish and subscribe to AWS IoT Core MQTT topics\.
+ Improves logging capabilities\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_1.1.1`  
+ Released 2021\.05\.05

**IDT v4\.0\.1 for AWS IoT Greengrass**    
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Version 2 software\.
+ Enables you to develop and run your custom test suites using AWS IoT Device Tester for AWS IoT Greengrass\. For more information, see [Use IDT to develop and run your own test suites](idt-custom-tests.md)\.
+ Provides code signed IDT applications for macOS and Windows\. On macOS, you might need to grant a security exception for IDT\. For more information, see [Security exception on macOS](idt-troubleshooting.md#macos-notarization-exception)\.  
Test suite version:    
`GGV2Q_1.0.0`  
+ Released 2020\.12\.22
+ The test suite runs only required tests for qualification, unless you set the corresponding `value` in the `features` array to `yes`\.
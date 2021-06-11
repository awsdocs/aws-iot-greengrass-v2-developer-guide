# Supported versions of AWS IoT Device Tester for AWS IoT Greengrass V2<a name="dev-test-versions"></a>

This topic lists supported versions of IDT for AWS IoT Greengrass V2\. As a best practice, we recommend that you use the latest version of IDT for AWS IoT Greengrass V2 that supports your target version of AWS IoT Greengrass V2\. New releases of AWS IoT Greengrass might require you to download a new version of IDT for AWS IoT Greengrass\. You receive a notification when you start a test run if IDT for AWS IoT Greengrass is not compatible with the version of AWS IoT Greengrass you are using\.

By downloading the software, you agree to the [AWS IoT Device Tester License Agreement](https://docs.aws.amazon.com/greengrass/v2/developerguide/idt-license.html)\.

**Note**  
<a name="unzip-package-to-local-drive"></a>IDT does not support being run by multiple users from a shared location, such as an NFS directory or a Windows network shared folder\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.

## Latest IDT version for AWS IoT Greengrass<a name="idt-latest-version"></a>

You can use this version of IDT for AWS IoT Greengrass with the AWS IoT Greengrass version listed here\. 

**IDT v4\.0\.2 for AWS IoT Greengrass**    
Supported AWS IoT Greengrass component versions:   
+ [Greengrass nucleus](greengrass-nucleus-component.md) v2\.1\.0 with [Greengrass CLI](greengrass-cli-component.md) v2\.1\.0  
IDT software downloads:  
+ IDT v4\.0\.2 with test suite GGV2Q\_1\.1\.0 for [Linux](https://docs.aws.amazon.com/greengrass/v2/developerguide/download-idt-gg2-4.0.2-testsuite-1.1.0-linux.html)
+ IDT v4\.0\.2 with test suite GGV2Q\_1\.1\.0 for [macOS](https://docs.aws.amazon.com/greengrass/v2/developerguide/download-idt-gg2-4.0.2-testsuite-1.1.0-mac.html)
+ IDT v4\.0\.2 with test suite GGV2Q\_1\.1\.0 for [Windows](https://docs.aws.amazon.com/greengrass/v2/developerguide/download-idt-gg2-4.0.2-testsuite-1.1.0-win.html)  
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Core software v2\.1\.0\.
+ Add support for Lambda and MQTT feature qualification for AWS IoT Greengrass Core software v2\.1\.0 and later versions\. You can now use IDT for AWS IoT Greengrass to validate that your core device can run Lambda functions and that the device can publish and subscribe to AWS IoT Core MQTT topics\.
+ Improve logging capabilities\.
+ Additional minor bug fixes\.  
Test suite version:    
`GGV2Q_1.1.0`  
+ Released 2021\.05\.05

## Earlier IDT versions for AWS IoT Greengrass<a name="idt-earlier-version"></a>

The following earlier version of IDT for AWS IoT Greengrass is also supported\. 

**IDT v4\.0\.1 for AWS IoT Greengrass**    
Supported AWS IoT Greengrass component versions:   
+ [Greengrass nucleus](greengrass-nucleus-component.md) v2\.0\.5 with [Greengrass CLI](greengrass-cli-component.md) v2\.0\.5  
IDT software downloads:  
+ IDT v4\.0\.1 with test suite GGV2Q\_1\.0\.0 for [Linux](https://docs.aws.amazon.com/greengrass/v2/developerguide/download-idt-gg2-linux-4.0.1.html)
+ IDT v4\.0\.1 with test suite GGV2Q\_1\.0\.0 for [macOS](https://docs.aws.amazon.com/greengrass/v2/developerguide/download-idt-gg2-mac-4.0.1.html)
+ IDT v4\.0\.1 with test suite GGV2Q\_1\.0\.0 for [Windows](https://docs.aws.amazon.com/greengrass/v2/developerguide/download-idt-gg2-win-4.0.1.html)  
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Version 2 software\.
+ Enables you to develop and run your custom test suites using AWS IoT Device Tester for AWS IoT Greengrass\. For more information, see [Use IDT to develop and run your own test suites](idt-custom-tests.md)\.
+ Provides code signed IDT applications for macOS and Windows\. On macOS, you might need to grant a security exception for IDT\. For more information, see [Security exception on macOS](idt-troubleshooting.md#macos-notarization-exception)\.  
Test suite version:    
`GGV2Q_1.0.0`  
+ Released 2020\.12\.22
+ The test suite runs only required tests for qualification, unless you set the corresponding `value` in the `features` array to `yes`\.
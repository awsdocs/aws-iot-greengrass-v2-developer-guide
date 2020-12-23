# Supported versions of AWS IoT Device Tester for AWS IoT Greengrass<a name="dev-test-versions"></a>

This topic lists supported versions of IDT for AWS IoT Greengrass\. As a best practice, we recommend that you use the latest version of IDT for AWS IoT Greengrass that supports your target version of AWS IoT Greengrass\. New releases of AWS IoT Greengrass might require you to download a new version of IDT for AWS IoT Greengrass\.

**Note**  
You receive a notification when you start a test run if IDT for AWS IoT Greengrass is not compatible with the version of AWS IoT Greengrass you are using\.

By downloading the software, you agree to the [AWS IoT Device Tester License Agreement](https://d232ctwt5kahio.cloudfront.net/greengrass/AWS%20IoT%20Device%20Tester%20License%20Agreement.pdf)\.

## Latest IDT version for AWS IoT Greengrass<a name="idt-latest-version"></a>

You can use this version of IDT for AWS IoT Greengrass with the AWS IoT Greengrass version listed here\. 

**IDT v4\.0\.1 for AWS IoT Greengrass**    
Supported AWS IoT Greengrass component versions:   
+ [Greengrass nucleus v2\.0\.3](greengrass-nucleus-component.md)
+ [Greengrass CLI v2\.0\.3](greengrass-cli-component.md)  
IDT software downloads:  
+ IDT v4\.0\.1 with test suite GGV2Q\_1\.0\.0 for [Linux](https://d232ctwt5kahio.cloudfront.net/greengrassv2/devicetester_greengrass_v2_linux_4.0.1.zip)
+ IDT v4\.0\.1 with test suite GGV2Q\_1\.0\.0 for [macOS](https://d232ctwt5kahio.cloudfront.net/greengrassv2/devicetester_greengrass_v2_mac_4.0.1.zip)
+ IDT v4\.0\.1 with test suite GGV2Q\_1\.0\.0 for [Windows](https://d232ctwt5kahio.cloudfront.net/greengrassv2/devicetester_greengrass_v2_win_4.0.1.zip)
<a name="unzip-package-to-local-drive"></a>IDT does not support being run by multiple users from a shared location, such as an NFS directory or a Windows network shared folder\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.  
Release notes:  
+ Enables you to validate and qualify devices running AWS IoT Greengrass Version 2 software\.
+ Enables you to develop and run your custom test suites using AWS IoT Device Tester for AWS IoT Greengrass\. For more information, see [Use IDT to develop and run your own test suites](idt-custom-tests.md)\.
+ Provides code signed IDT applications for macOS and Windows\. On macOS, you might need to grant a security exception for IDT\. For more information, see [Security exception on macOS](idt-troubleshooting.md#macos-notarization-exception)\.  
Test suite version:    
`GGV2Q_1.0.0`  
+ Released 2020\.12\.22
+ The test suite runs only required tests for qualification, unless you set the corresponding `value` in the `features` array to `yes`\.
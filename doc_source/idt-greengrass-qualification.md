# Use IDT to run the AWS IoT Greengrass qualification suite<a name="idt-greengrass-qualification"></a>

You can use AWS IoT Device Tester for AWS IoT Greengrass to verify that the AWS IoT Greengrass Core software runs on your hardware and can communicate with the AWS Cloud\. It also performs end\-to\-end tests with AWS IoT Core\. For example, it verifies that your device can deploy components and upgrade them\. 

In addition to testing devices, IDT for AWS IoT Greengrass creates resources \(for example, AWS IoT things, groups, and so on\) in your AWS account to facilitate the qualification process\.

<a name="idt-aws-credentials"></a>To create these resources, IDT for AWS IoT Greengrass uses the AWS credentials configured in the `config.json` file to make API calls on your behalf\. These resources are provisioned at various times during a test\.

When you use IDT for AWS IoT Greengrass to run the AWS IoT Greengrass qualification suite, it performs the following steps:

1. Loads and validates your device and credentials configuration\.

1. Performs selected tests with the required local and cloud resources\.

1. Cleans up local and cloud resources\.

1. Generates tests reports that indicate if your board passed the tests required for qualification\.

## Test suite versions<a name="idt-test-suite-versions"></a>

IDT for AWS IoT Greengrass organizes tests into test suites and test groups\.<a name="idt-test-suites-groups"></a>
+ A test suite is the set of test groups used to verify that a device works with particular versions of AWS IoT Greengrass\.
+ A test group is the set of individual tests related to a particular feature, such as component deployments\.

Test suites are versioned using a `major.minor.patch` format, for example `GGV2Q_1.0.0`\. When you download IDT, the package includes the latest Greengrass qualification suite version\.

**Important**  
IDT supports the four latest `major.minor` versions of the Greengrass qualification suite versions to generate qualification reports that you can submit to AWS Partner Network to include your devices in the AWS Partner Device Catalog\. Tests from unsupported test suite versions are not valid for device qualification\. IDT doesn't print qualification reports for unsupported versions\. For more information, see [Support policy for AWS IoT Device Tester for AWS IoT Greengrass](idt-support-policy.md)\.  
You can run `list-supported-products` to list the versions of AWS IoT Greengrass and test suites that are supported by your current version of IDT\. 

## Test group descriptions<a name="dt-test-groups"></a>

------
#### [ IDT v4\.0\.2 and later ]

**Required Test Groups for Core Qualification**  
These test groups are required to qualify your AWS IoT Greengrass V2 device for the AWS Partner Device Catalog\.    
Core Dependencies  
Validates that your device meets all software and hardware requirements for the AWS IoT Greengrass Core software\. This test group includes the following test case:     
Java Version  
Checks that the required Java version is installed on your device\. AWS IoT Greengrass requires Java 8 or later\.  
PreTest Validation  
Checks that your device can run the following Linux commands:   
`chmod`, `cp`, `echo`, `grep`, `kill`, `ln`, `mkinfo`, `ps`, `rm`, `sh`, `uname`   
Version Checker  
Checks that the version of AWS IoT Greengrass provided is compatible with the AWS IoT Device Tester version you are using\.  
Component  
Validates that your device can deploy components and upgrade them\. This test group includes the following tests:    
Cloud Component  
Validates device capability for cloud components\.  
Local Component  
Validates device capability for local components\.  
Lambda  
Validates that your device can deploy Lambda function components that use the Java runtime, and that the Lambda functions can use AWS IoT Core MQTT topics as event sources for work messages\.  
MQTT  
Validates that your device can subscribe and publish to AWS IoT Core MQTT topics\.

------
#### [ IDT v4\.0\.1 ]

**Required Test Groups for Core Qualification**  
These test groups are required to qualify your AWS IoT Greengrass V2 device for the AWS Partner Device Catalog\.    
Core Dependencies  
Validates that your device meets all software and hardware requirements for the AWS IoT Greengrass Core software\. This test group includes the following test case:     
Java Version  
Checks that the required Java version is installed on your device\. AWS IoT Greengrass requires Java 8 or later\.  
PreTest Validation  
Checks that your device can run the following Linux commands:   
`chmod`, `cp`, `echo`, `grep`, `kill`, `ln`, `mkinfo`, `ps`, `rm`, `sh`, `uname`   
Version Checker  
Checks that the version of AWS IoT Greengrass provided is compatible with the AWS IoT Device Tester version you are using\.  
Component  
Validates that your device can deploy components and upgrade them\. This test group includes the following tests:    
Cloud Component  
Validates device capability for cloud components\.  
Local Component  
Validates device capability for local components\.

------
# Use IDT to run the AWS IoT Greengrass qualification suite<a name="idt-greengrass-qualification"></a>

You can use AWS IoT Device Tester for AWS IoT Greengrass V2 to verify that the AWS IoT Greengrass Core software runs on your hardware and can communicate with the AWS Cloud\. It also performs end\-to\-end tests with AWS IoT Core\. For example, it verifies that your device can deploy components and upgrade them\. 

In addition to testing devices, IDT for AWS IoT Greengrass V2 creates resources \(for example, AWS IoT things, groups, and so on\) in your AWS account to facilitate the qualification process\.

<a name="idt-aws-credentials"></a>To create these resources, IDT for AWS IoT Greengrass V2 uses the AWS credentials configured in the `config.json` file to make API calls on your behalf\. These resources are provisioned at various times during a test\.

When you use IDT for AWS IoT Greengrass V2 to run the AWS IoT Greengrass qualification suite, it performs the following steps:

1. Loads and validates your device and credentials configuration\.

1. Performs selected tests with the required local and cloud resources\.

1. Cleans up local and cloud resources\.

1. Generates tests reports that indicate if your board passed the tests required for qualification\.

## Test suite versions<a name="idt-test-suite-versions"></a>

IDT for AWS IoT Greengrass V2 organizes tests into test suites and test groups\.<a name="idt-test-suites-groups"></a>
+ A test suite is the set of test groups used to verify that a device works with particular versions of AWS IoT Greengrass\.
+ A test group is the set of individual tests related to a particular feature, such as component deployments\.

Test suites are versioned using a `major.minor.patch` format, for example `GGV2Q_1.0.0`\. When you download IDT, the package includes the latest Greengrass qualification suite version\.

**Important**  
IDT supports the four latest `major.minor` versions of the Greengrass qualification suite versions to generate qualification reports that you can submit to AWS Partner Network to include your devices in the AWS Partner Device Catalog\. Tests from unsupported test suite versions are not valid for device qualification\. IDT doesn't print qualification reports for unsupported versions\. For more information, see [Support policy for AWS IoT Device Tester for AWS IoT Greengrass](idt-support-policy.md)\.  
You can run `list-supported-products` to list the versions of AWS IoT Greengrass and test suites that are supported by your current version of IDT\. 

## Test group descriptions<a name="dt-test-groups"></a>

**Required Test Groups for Core Qualification**  
These test groups are required to qualify your AWS IoT Greengrass V2 device for the AWS Partner Device Catalog\.    
Core Dependencies  
Validates that the device meets all software and hardware requirements for the AWS IoT Greengrass Core software\. This test group includes the following test case:     
Java Version  
Checks that the required Java version is installed on the device under test\. AWS IoT Greengrass requires Java 8 or later\.  
PreTest Validation  
Checks that the device meets the software requirements to run tests\.   
+ For Linux\-based devices, this test checks if the device can run the following Linux commands: 

  `chmod`, `cp`, `echo`, `grep`, `kill`, `ln`, `mkinfo`, `ps`, `rm`, `sh`, `uname` 
+ For Windows\-based devices, this test checks if the device has the following Microsoft software installed:

  [Powershell](https://docs.microsoft.com/en-us/powershell/?view=powershell-7.1) v5\.1 or later, [\.NET](https://docs.microsoft.com/en-us/dotnet/fundamentals/powershell) v4\.6\.1 or later, [Visual C\+\+](https://docs.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-170) 2017 or later, [PsExec utility](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)  
Version Checker  
Checks that the version of AWS IoT Greengrass provided is compatible with the AWS IoT Device Tester version you are using\.  
Component  
Validates that the device can deploy components and upgrade them\. This test group includes the following tests:    
Cloud Component  
Validates device capability for cloud components\.  
Local Component  
Validates device capability for local components\.  
Lambda  
This test is not applicable for Windows\-based devices\.  
Validates that the device can deploy Lambda function components that use the Java runtime, and that the Lambda functions can use AWS IoT Core MQTT topics as event sources for work messages\.  
MQTT  
Validates that the device can subscribe and publish to AWS IoT Core MQTT topics\.

**Optional Test Groups**  
These test groups are optional, and used only for qualifying Linux\-based Greengrass core devices\. If you choose to qualify for optional tests, your device is listed with additional capabilities in the AWS Partner Device Catalog\.  
Docker dependencies  
<a name="description-docker"></a>Validates that the device meets all required technical dependencies to use the AWS\-provided Docker application manager \(`aws.greengrass.DockerApplicationManager`\) component\.  
Docker Application Manager Qualification  
<a name="description-docker-app-manager-qual"></a>Validates <a name="description-docker-app-manager-qual-phrase"></a>that the device can download a Docker container image from Amazon ECR \.  
Machine Learning Dependencies  
<a name="description-ml"></a>Validates that the device meets all of the required technical dependencies to use the AWS\-provided machine learning \(ML\) components\.  
Machine Learning Inference Tests  
<a name="description-ml-inference"></a>Validates <a name="description-ml-inference-phrase"></a>that the device can perform ML inference using the [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) and [TensorFlow Lite](https://www.tensorflow.org/lite/guide/python) ML frameworks\.  
Stream Manager Dependencies  
<a name="description-sm"></a>Validates that the device can download, install, and run the [AWS IoT Greengrass stream manager](manage-data-streams.md)\.  
Hardware Security Integration \(HSI\)  
This test is available in IDT v4\.5\.1 and later for Linux\-based devices only\. AWS IoT Greengrass doesn't currently support hardware security integration for Windows devices\.
<a name="description-hsi"></a>Validates that the device can authenticate connections to the AWS IoT and AWS IoT Greengrass services using a private key and certificate that are stored in a hardware security module \(HSM\)\. This test also verifies that the AWS\-provided [PKCS\#11 provider component](pkcs11-provider-component.md) can interface with the HSM using a vendor\-provided PKCS\#11 library\. For more information, see [Hardware security integration](hardware-security.md)\.
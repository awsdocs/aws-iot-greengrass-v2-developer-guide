# AWS IoT Greengrass Development Kit Command\-Line Interface<a name="greengrass-development-kit-cli"></a>

The AWS IoT Greengrass Development Kit Command\-Line Interface \(GDK CLI\) provides features that help you develop [custom Greengrass components](develop-greengrass-components.md)\. You can use the GDK CLI to create, build, and publish custom components\. When you create a component repository with the GDK CLI, you can start from a template or a community component from the [Greengrass Software Catalog](greengrass-software-catalog.md)\. Then, you can choose a build system that packages files as ZIP archives, uses a Maven or Gradle build script, or runs a custom build command\. After you create a component, you can use the GDK CLI to publish it to the AWS IoT Greengrass service, so you can use the AWS IoT Greengrass console or API to deploy the component to your Greengrass core devices\.

When you develop Greengrass components without the GDK CLI, you must update the version and artifact URIs in the [component recipe file](component-recipe-reference.md) each time you create a new version of the component\. When you use the GDK CLI, it can automatically update the version and artifact URIs for you each time you publish a new version of the component\.

The GDK CLI is open source and available on GitHub\. You can customize and extend the GDK CLI to meet your component development needs\. We invite you to open issues and pull requests on the GitHub repository\. You can find the GDK CLI source at the following link: [https://github\.com/aws\-greengrass/aws\-greengrass\-gdk\-cli](https://github.com/aws-greengrass/aws-greengrass-gdk-cli)\.

## Prerequisites<a name="gdk-cli-prerequisites"></a>

To install and use the Greengrass Development Kit CLI, you need the following:
+ An AWS account\. If you don't have one, see [Set up an AWS account](setting-up.md#set-up-aws-account)\.
+ A Windows, macOS, or Unix\-like development computer with an internet connection\.
+ For GDK CLI version 1\.1\.0 or later, [Python](https://www.python.org/downloads/) 3\.6 or later installed on your development computer\.

  For GDK CLI version 1\.0\.0, [Python](https://www.python.org/downloads/) 3\.8 or later installed on your development computer\.
+ [Git](https://git-scm.com/) installed on your development computer\.
+ <a name="development-component-aws-cli-prerequisite"></a>AWS Command Line Interface \(AWS CLI\) installed and configured with credentials on your development computer\. For more information, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS Command Line Interface User Guide*\.
**Note**  
If you use a Raspberry Pi or another 32\-bit ARM device, install AWS CLI V1\. AWS CLI V2 isn't available for 32\-bit ARM devices\. For more information, see [Installing, updating, and uninstalling the AWS CLI version 1](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv1.html)\.
+ To use the GDK CLI to publish components to the AWS IoT Greengrass service, you must have the following permissions:
  + `s3:CreateBucket`
  + `s3:PutObject`
  + `greengrass:CreateComponentVersion`
  + `greengrass:ListComponentVersions`
+ To use the GDK CLI to build a component whose artifacts exist in an S3 bucket and not in the local file system, you must have the `s3:ListBucket` permission\.

  This feature is available for GDK CLI v1\.1\.0 and later\.

## Changelog<a name="gdk-cli-changelog"></a>

The following table describes the changes in each version of the GDK CLI\. For more information, see the [GDK CLI Releases page](https://github.com/aws-greengrass/aws-greengrass-gdk-cli/releases) on GitHub\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  1\.1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-development-kit-cli.html)  | 
|  1\.0\.0  |  Initial version\.  | 
# Setting up AWS IoT Greengrass core devices<a name="setting-up"></a>

Complete the tasks in this section to install, configure, and run the AWS IoT Greengrass Core software\.

**Note**  
This section describes advanced installation and configuration of the AWS IoT Greengrass Core software\. If you're a first\-time user of AWS IoT Greengrass V2, we recommend that you first complete the [getting started tutorial](getting-started.md) to set up a core device and explore the features of AWS IoT Greengrass\.



## Supported platforms and requirements<a name="installation-requirements"></a>

Before you begin, make sure you meet the following requirements to install and run the AWS IoT Greengrass Core software\.

**Tip**  
You can search for devices that are qualified for AWS IoT Greengrass V2 in the [AWS Partner Device Catalog](https://devices.amazonaws.com/search?kw=%22HSI%22&page=1)\.

**Topics**
+ [Supported platforms](#greengrass-v2-supported-platforms)
+ [Device requirements](#greengrass-v2-requirements)
+ [Lambda function requirements](#greengrass-v2-lambda-requirements)

### Supported platforms<a name="greengrass-v2-supported-platforms"></a>

AWS IoT Greengrass officially supports devices running the following platforms\. Devices with platforms not included in this list might work, but AWS IoT Greengrass tests on only these specified platforms\.

------
#### [ Linux ]

Architectures:
+ Armv7l
+ Armv8 \(AArch64\)
+ x86\_64

------
#### [ Windows ]

Architectures:
+ x86\_64

Versions:
+ Windows 10
+ Windows Server 2019

**Note**  
Some AWS IoT Greengrass features aren't currently supported on Windows devices\. For more information, see [Greengrass feature compatibility by operating system](operating-system-feature-support-matrix.md) and [Feature considerations for Windows devices](#greengrass-v2-windows-feature-considerations)\.

------

Linux platforms can also run AWS IoT Greengrass V2 in a Docker container\. For more information, see [Run AWS IoT Greengrass Core software in a Docker container](run-greengrass-docker.md)\. 

To build a custom Linux\-based operating system, you can use the BitBake recipe for AWS IoT Greengrass V2 in the [`meta-aws` project](https://github.com/aws/meta-aws/tree/master/recipes-iot)\. The `meta-aws` project provides recipes that you can use to build AWS edge software capabilities in [embedded Linux](https://elinux.org/) systems that are built with [OpenEmbedded](https://www.openembedded.org/wiki/Main_Page) and Yocto Project build frameworks\. The [Yocto Project](https://www.yoctoproject.org/) is an open source collaboration project that helps you build custom Linux\-based systems for embedded applications regardless of hardware architecture\. The BitBake recipe for AWS IoT Greengrass V2 installs, configures, and automatically runs the AWS IoT Greengrass Core software on your device\.

### Device requirements<a name="greengrass-v2-requirements"></a>

Devices must meet the following requirements to install and run the AWS IoT Greengrass Core software v2\.x\.

**Note**  
You can use AWS IoT Device Tester for AWS IoT Greengrass to verify that your device can run the AWS IoT Greengrass Core software and communicate with the AWS Cloud\. For more information, see [Using AWS IoT Device Tester for AWS IoT Greengrass V2](device-tester-for-greengrass-ug.md)\.

------
#### [ Linux ]
+ Minimum 256 MB disk space available for the AWS IoT Greengrass Core software\. This requirement doesn't include components deployed to the core device\.
+ Minimum 96 MB RAM allocated to the AWS IoT Greengrass Core software\. This requirement doesn't include components that run on the core device\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.
+ Java Runtime Environment \(JRE\) version 8 or greater\. To use Java to develop custom components, you must install a Java Development Kit \(JDK\)\. We recommend that you use [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\.
+ [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.25 or greater\.
+ You must run the AWS IoT Greengrass Core software as a root user\. Use `sudo`, for example\.
+ The root user that runs the AWS IoT Greengrass Core software, such as `root`, must have permission to run `sudo` with any user and any group\. The `/etc/sudoers` file must give this user permission to run `sudo` as other groups\. The permission for the user in `/etc/sudoers` should look like the following example\.

  ```
  root    ALL=(ALL:ALL) ALL
  ```
+ The core device must be able to perform outbound requests to a set of endpoints and ports\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.
+ The `/tmp` directory must be mounted with `exec` permissions\.
+ All of the following shell commands:
  + `ps -ax -o pid,ppid`
  + `sudo`
  + `sh`
  + `kill`
  + `cp`
  + `chmod`
  + `rm`
  + `ln`
  + `echo`
  + `exit`
  + `id`
  + `uname`
  + `grep`
+ Your device may also require the following optional shell commands:
  + \(Optional\) `systemctl` \(to set up the AWS IoT Greengrass Core software as a system service\)
  + \(Optional\) `useradd`, `groupadd`, and `usermod` \(to set up the `ggc_user` system user and `ggc_group` system group\)
  + \(Optional\) `mkfifo` \(to run Lambda functions as components\)
+ To configure system resource limits for component processes, your device must run Linux kernel version 2\.6\.24 or later\.
+ To run Lambda functions, your device must meet additional requirements\. For more information, see [Lambda function requirements](#greengrass-v2-lambda-requirements)\.

------
#### [ Windows ]
+ Minimum 256 MB disk space available for the AWS IoT Greengrass Core software\. This requirement doesn't include components deployed to the core device\.
+ Minimum 160 MB RAM allocated to the AWS IoT Greengrass Core software\. This requirement doesn't include components that run on the core device\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.
+ Java Runtime Environment \(JRE\) version 8 or greater\. To use Java to develop custom components, you must install a Java Development Kit \(JDK\)\. We recommend [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\. 
**Note**  
To use version 2\.5\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md), you must use a 64\-bit version of the Java Runtime Environment \(JRE\)\. Greengrass nucleus version 2\.5\.1 supports 32\-bit and 64\-bit JREs\.
+ The user who installs the AWS IoT Greengrass Core software must be an administrator\.
+ Each user that runs component processes must exist in the LocalSystem account, and the user's name and password must be in the Credential Manager instance for the LocalSystem account\. You can set up this user when you follow instructions to [install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\.
+ The core device must be able to perform outbound requests to a set of endpoints and ports\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.

------

### Lambda function requirements<a name="greengrass-v2-lambda-requirements"></a>

Your device must meet the following requirements to run Lambda functions:
+ A Linux\-based operating system\.
+ Your device must have the `mkfifo` shell command\.
+ Your device must run the programming language libraries that a Lambda function requires\. You must install the required libraries on the device and add them to the `PATH` environment variable\.
  + [Python](https://www.python.org/) version 3\.8 for functions that use the Python 3\.8 runtime\.
  + [Python](https://www.python.org/) version 3\.7 for functions that use the Python 3\.7 runtime\.
  + [Python](https://www.python.org/) version 2\.7 for functions that use the Python 2\.7 runtime\.
  + [Node\.js](https://www.nodejs.org/) version 12\.x for functions that use the Node\.js 12\.x runtime\.
  + [Node\.js](https://www.nodejs.org/) version 10\.x for functions that use the Node\.js 10\.x runtime\.
  + [Java](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) version 8 or later for functions that use the Java 8 runtime\.

  For more information about AWS IoT Greengrass support for Lambda runtimes, see [Run AWS Lambda functions](run-lambda-functions.md)\.
+ To run containerized Lambda functions, your device must meet the following requirements:
  + Linux kernel version 4\.4 or later\.
  + The kernel must support [cgroups](https://en.wikipedia.org/wiki/Cgroups) v1, and you must enable and mount the following cgroups:
    + The *memory* cgroup for AWS IoT Greengrass to set the memory limit for containerized Lambda functions\.
    + The *devices* cgroup for containerized Lambda functions to access system devices or volumes\.

    The AWS IoT Greengrass Core software doesn't support cgroups v2\.

    To meet this requirement, boot the device with the following Linux kernel parameters\.

    ```
    cgroup_enable=memory cgroup_memory=1 systemd.unified_cgroup_hierarchy=0
    ```
**Tip**  
On a Raspberry Pi, edit the `/boot/cmdline.txt` file to set the device's kernel parameters\.
  + You must enable the following Linux kernel configurations on the device:
    + Namespace:
      + `CONFIG_IPC_NS`
      + `CONFIG_UTS_NS`
      + `CONFIG_USER_NS`
      + `CONFIG_PID_NS`
    + Cgroups:
      + `CONFIG_CGROUP_DEVICE`
      + `CONFIG_CGROUPS`
      + `CONFIG_MEMCG`
    + Others:
      + `CONFIG_POSIX_MQUEUE`
      + `CONFIG_OVERLAY_FS`
      + `CONFIG_HAVE_ARCH_SECCOMP_FILTER`
      + `CONFIG_SECCOMP_FILTER`
      + `CONFIG_KEYS`
      + `CONFIG_SECCOMP`
      + `CONFIG_SHMEM`

## Feature considerations for Windows devices<a name="greengrass-v2-windows-feature-considerations"></a>

Some AWS IoT Greengrass features aren't currently supported on Windows devices\. Review the following considerations to confirm if a Windows device satisfies your feature requirements\. For more information, see [Greengrass feature compatibility by operating system](operating-system-feature-support-matrix.md)\.
+ You can't run Lambda functions\.
+ You can't configure system resource limits to customize the maximum amount of CPU and RAM usage that each component's processes can use on a core device\.
+ You can't pause and resume component processes using the [PauseComponent](ipc-component-lifecycle.md#ipc-operation-pausecomponent) and [ResumeComponent](ipc-component-lifecycle.md#ipc-operation-resumecomponent) interprocess communication operations\.
+ The following [AWS\-provided components](public-components.md) are not currently supported: 
  + Kinesis Data Firehose
  + Lambda launcher
  + Lambda manager
  + Lambda runtimes
  + Legacy subscription router
  + Modbus\-RTU protocol adapter
  + Secure tunneling
  + Amazon SNS
  + AWS IoT SiteWise OPC\-UA collector
  + AWS IoT SiteWise publisher
  + AWS IoT SiteWise processor

## Set up an AWS account<a name="set-up-aws-account"></a>

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user that follows and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add users**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \- job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.
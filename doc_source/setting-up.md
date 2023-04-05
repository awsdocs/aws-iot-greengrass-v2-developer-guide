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
+ Windows 11
+ Windows Server 2019
+ Windows Server 2022

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
+ <a name="requirement-supported-region"></a>The use of an [AWS Region](https://en.wikipedia.org/wiki/Amazon_Web_Services#Availability_and_topology) that supports AWS IoT Greengrass V2\. For the list of supported Regions, see [AWS IoT Greengrass V2 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/greengrassv2.html) in the *AWS General Reference*\.
+ Minimum 256 MB disk space available for the AWS IoT Greengrass Core software\. This requirement doesn't include components deployed to the core device\.
+ Minimum 96 MB RAM allocated to the AWS IoT Greengrass Core software\. This requirement doesn't include components that run on the core device\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.
+ Java Runtime Environment \(JRE\) version 8 or greater\. Java must be available on the [PATH](https://en.wikipedia.org/wiki/PATH_(variable)) environment variable on the device\. To use Java to develop custom components, you must install a Java Development Kit \(JDK\)\. We recommend that you use [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\.
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
  + \(Optional\) `systemctl`\. This command is used to set up the AWS IoT Greengrass Core software as a system service\.
  + \(Optional\) `useradd`, `groupadd`, and `usermod`\. These command are used to set up the `ggc_user` system user and `ggc_group` system group\.
  + \(Optional\) `mkfifo`\. This command is used to run Lambda functions as components\.
+ To configure system resource limits for component processes, your device must run Linux kernel version 2\.6\.24 or later\.
+ To run Lambda functions, your device must meet additional requirements\. For more information, see [Lambda function requirements](#greengrass-v2-lambda-requirements)\.

------
#### [ Windows ]
+ <a name="requirement-supported-region"></a>The use of an [AWS Region](https://en.wikipedia.org/wiki/Amazon_Web_Services#Availability_and_topology) that supports AWS IoT Greengrass V2\. For the list of supported Regions, see [AWS IoT Greengrass V2 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/greengrassv2.html) in the *AWS General Reference*\.
+ Minimum 256 MB disk space available for the AWS IoT Greengrass Core software\. This requirement doesn't include components deployed to the core device\.
+ Minimum 160 MB RAM allocated to the AWS IoT Greengrass Core software\. This requirement doesn't include components that run on the core device\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.
+ Java Runtime Environment \(JRE\) version 8 or greater\. Java must be available on the [PATH](https://en.wikipedia.org/wiki/PATH_(variable)) system variable on the device\. To use Java to develop custom components, you must install a Java Development Kit \(JDK\)\. We recommend [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\. 
**Note**  
To use version 2\.5\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md), you must use a 64\-bit version of the Java Runtime Environment \(JRE\)\. Greengrass nucleus version 2\.5\.1 supports 32\-bit and 64\-bit JREs\.
+ The user who installs the AWS IoT Greengrass Core software must be an administrator\.
+ You must install the AWS IoT Greengrass Core software as a system service\. Specify `--setup-system-service true` when you install the software\.
+ Each user that runs component processes must exist in the LocalSystem account, and the user's name and password must be in the Credential Manager instance for the LocalSystem account\. You can set up this user when you follow instructions to [install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\.
+ The core device must be able to perform outbound requests to a set of endpoints and ports\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.

------

### Lambda function requirements<a name="greengrass-v2-lambda-requirements"></a>

Your device must meet the following requirements to run Lambda functions:
+ A Linux\-based operating system\.
+ Your device must have the `mkfifo` shell command\.
+ Your device must run the programming language libraries that a Lambda function requires\. You must install the required libraries on the device and add them to the `PATH` environment variable\.
  + [Python](https://www.python.org/) version 3\.9 for functions that use the Python 3\.9 runtime\.
  + [Python](https://www.python.org/) version 3\.8 for functions that use the Python 3\.8 runtime\.
  + [Python](https://www.python.org/) version 3\.7 for functions that use the Python 3\.7 runtime\.
  + [Python](https://www.python.org/) version 2\.7 for functions that use the Python 2\.7 runtime\.
  + [Node\.js](https://www.nodejs.org/) version 14\.x for functions that use the Node\.js 14\.x runtime\.
  + [Node\.js](https://www.nodejs.org/) version 12\.x for functions that use the Node\.js 12\.x runtime\.
  + [Node\.js](https://www.nodejs.org/) version 10\.x for functions that use the Node\.js 10\.x runtime\.
  + [Java](https://www.oracle.com/java/technologies/downloads/#java11) version 11 or later for functions that use the Java 11 runtime\.
  + [Java](https://www.oracle.com/java/technologies/downloads/#java8) version 8 or later for functions that use the Java 8 runtime\.

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
**Tip**  
Check the documentation for your Linux distribution to learn how to verify and set Linux kernel parameters\. You can also use AWS IoT Device Tester for AWS IoT Greengrass to verify that your device meets these requirements\. For more information, see [Using AWS IoT Device Tester for AWS IoT Greengrass V2](device-tester-for-greengrass-ug.md)\.

## Feature considerations for Windows devices<a name="greengrass-v2-windows-feature-considerations"></a>

Some AWS IoT Greengrass features aren't currently supported on Windows devices\. Review the feature differences to confirm if a Windows device satisfies your requirements\. For more information, see [Greengrass feature compatibility by operating system](operating-system-feature-support-matrix.md)\.

## Set up an AWS account<a name="set-up-aws-account"></a>

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/accounts/latest/reference/root-user-tasks.html)\.

To create an administrator user, choose one of the following options\.


****  

| Choose one way to manage your administrator | To | By | You can also | 
| --- | --- | --- | --- | 
| In IAM Identity Center \(Recommended\) | Use short\-term credentials to access AWS\.This aligns with the security best practices\. For information about best practices, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-users-federation-idp) in the *IAM User Guide*\. | Following the instructions in [Getting started](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html) in the AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide\. | Configure programmatic access by [Configuring the AWS CLI to use AWS IAM Identity Center \(successor to AWS Single Sign\-On\)](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) in the AWS Command Line Interface User Guide\. | 
| In IAM \(Not recommended\) | Use long\-term credentials to access AWS\. | Following the instructions in [Creating your first IAM admin user and user group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the IAM User Guide\. | Configure programmatic access by [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 
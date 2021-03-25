# Setting up AWS IoT Greengrass core devices<a name="setting-up"></a>

Complete the tasks in this section to install, configure, and run the AWS IoT Greengrass Core software V2\.

**Note**  
This section describes advanced configuration of the AWS IoT Greengrass Core software\. If you're a first\-time user of AWS IoT Greengrass V2, we recommend that you complete the [getting started tutorial](getting-started.md) to set up a core device and explore the features of AWS IoT Greengrass\.

**Topics**
+ [Requirements](#greengrass-v2-requirements)
+ [Set up an AWS account](#set-up-aws-account)
+ [Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)
+ [Run the AWS IoT Greengrass Core software](run-greengrass-core-v2.md)
+ [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)
+ [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)

## Requirements<a name="greengrass-v2-requirements"></a>

Devices must meet the following requirements to install and run the AWS IoT Greengrass Core software v2\.0:
+ One of the following supported platforms:<a name="greengrass-supported-platforms"></a>
  + Architecture: Armv7l
    + OS: Linux
  + Architecture: Armv8 \(AArch64\)
    + OS: Linux
  + Architecture: x86\_64
    + OS: Linux
**Note**  
You can use AWS IoT Device Tester for AWS IoT Greengrass to verify that the AWS IoT Greengrass Core software runs on your hardware and can communicate with the AWS Cloud\. For more information, see [Using AWS IoT Device Tester for AWS IoT Greengrass V2](device-tester-for-greengrass-ug.md)\.
+ Minimum 256 MB disk space available for the AWS IoT Greengrass Core software\. This requirement doesn't include components deployed to the core device\.
+ Minimum 128 MB RAM allocated to the AWS IoT Greengrass Core software\. This requirement doesn't include components that run on the core device\.
+ Java version 8 or greater\. We recommend [OpenJDK](https://openjdk.java.net/) or [Amazon Corretto](http://aws.amazon.com/corretto/)\.
+ [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.25 or greater\.
+ The user that runs the AWS IoT Greengrass Core software \(typically `root`\), must have permission to run `sudo` with any user and any group\. The `/etc/sudoers` file must give this user permission to run `sudo` as other groups\. The permission for the user in `/etc/sudoers` should look like the following example\.

  ```
  root    ALL=(ALL:ALL) ALL
  ```
+ The `/tmp` directory must be mounted with `exec` permissions\.
+ All of the following shell commands:
  + `ps`
  + `sudo`
  + `sh`
  + `kill`
  + `cp`
  + `chmod`
  + `rm`
  + `ln`
  + `echo`
  + `exit`
  + `uname`
  + `grep`
+ Your device may also require the following optional shell commands:
  + \(Optional\) `systemctl` \(to set up the AWS IoT Greengrass Core software as a system service\)
  + \(Optional\) `mkfifo` \(to run Lambda functions as components\)
  + \(Optional\) `useradd` and `groupadd` to setup the `ggc_user` and `ggc_group` on install
  + \(Optional\) `id` and `usermod` to verify `ggc_user` and `ggc_group` on startup
+ To run Lambda functions, your device must meet additional requirements\. For more information, see [Requirements to run Lambda functions](#greengrass-v2-lambda-requirements)\.

### Requirements to run Lambda functions<a name="greengrass-v2-lambda-requirements"></a>

Your device must meet the following requirements to run Lambda functions:
+ You must run the AWS IoT Greengrass Core software as a root user\. Use `sudo`, for example\.
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
  + The kernel must support [cgroups](https://en.wikipedia.org/wiki/Cgroups), and you must enable and mount the following cgroups:
    + The *memory* cgroup for AWS IoT Greengrass to set the memory limit for containerized Lambda functions\.
    + The *devices* cgroup for containerized Lambda functions to access system devices or volumes\.
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

## Set up an AWS account<a name="set-up-aws-account"></a>

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add user**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \-job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

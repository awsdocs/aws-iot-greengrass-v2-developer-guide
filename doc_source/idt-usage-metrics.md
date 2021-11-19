# IDT usage metrics<a name="idt-usage-metrics"></a>

If you provide AWS credentials with required permissions, AWS IoT Device Tester collects and submits usage metrics to AWS\. This is an opt\-in feature and is used to improve IDT functionality\. IDT collects information such as the following: 
+ The AWS account ID used to run IDT
+  The IDT CLI commands used to run tests
+ The test suite that are run
+ The test suites in the *<device\-tester\-extract\-location>* folder
+ The number of devices configured in the device pool
+ Test case names and run times
+ Test result information, such as whether tests passed, failed, encountered errors, or were skipped
+ Product features tested
+ IDT exit behavior, such as unexpected or early exits 

 All of the information that IDT sends is also logged to a `metrics.log` file in the `<device-tester-extract-location>/results/<execution-id>/` folder\. You can view the log file to see the information that was collected during a test run\. This file is generated only if you choose to collect usage metrics\. 

To disable metrics collection, you do not need to take additional action\. Simply do not store your AWS credentials, and if you do have stored AWS credentials, do not configure the `config.jso`n file to access them\.

## Configure your AWS credentials<a name="configure-aws-creds-for-metrics"></a>

If you do not already have an AWS account, you must [create one](#idt-metrics-aws-account)\. If you already have an AWS account, you simply need to [configure the required permissions](#idt-metrics-permissions) for your account that allow IDT to send usage metrics to AWS on your behalf\.

### Step 1: Create an AWS account<a name="idt-metrics-aws-account"></a>

In this step, create and configure an AWS account\. If you already have an AWS account, skip to [Step 2: Configure permissions for IDT](#idt-metrics-permissions)\.

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user that follows and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add user**\.

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

### Step 2: Configure permissions for IDT<a name="idt-metrics-permissions"></a>

In this step, configure the permissions that IDT uses to run tests and collect IDT usage data\. You can use the AWS Management Console or AWS Command Line Interface \(AWS CLI\) to create an IAM policy and a user for IDT, and then attach policies to the user\.
+ [To Configure Permissions for IDT \(Console\)](#idt-metrics-permissions-console)
+ [To Configure Permissions for IDT \(AWS CLI\)](#idt-metrics-permissions-cli)<a name="idt-metrics-permissions-console"></a>

**To configure permissions for IDT \(console\)**

Follow these steps to use the console to configure permissions for IDT for AWS IoT Greengrass\.

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam)\.

1. Create a customer managed policy that grants permissions to create roles with specific permissions\. 

   1. In the navigation pane, choose **Policies**, and then choose **Create policy**\.

   1. On the **JSON** tab, replace the placeholder content with the following policy\.

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "iot-device-tester:SendMetrics"
                  ],
                  "Resource": "*"
              }
          ]
      }
      ```

   1. Choose **Review policy**\.

   1. For **Name**, enter **IDTUsageMetricsIAMPermissions**\. Under **Summary**, review the permissions granted by your policy\.

   1. Choose **Create policy**\.

1. Create an IAM user and attach permissions to the user\.

   1. Create an IAM user\. Follow steps 1 through 5 in [Creating IAM users \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console) in the *IAM User Guide*\. If you already created an IAM user, skip to the next step\. 

   1. Attach the permissions to your IAM user:

      1. On the **Set permissions ** page, choose **Attach existing policies to user directly**\.

      1. Search for the **IDTUsageMetricsIAMPermissions** policy that you created in the previous step\. Select the check box\.

   1. Choose **Next: Tags**\.

   1. Choose **Next: Review** to view a summary of your choices\.

   1. Choose **Create user**\.

   1. To view the user's access keys \(access key IDs and secret access keys\), choose **Show** next to the password and access key\. To save the access keys, choose **Download\.csv** and save the file to a secure location\. You use this information later to configure your AWS credentials file\.

 <a name="idt-metrics-permissions-cli"></a>

**To configure permissions for IDT \(AWS CLI\)**

Follow these steps to use the AWS CLI to configure permissions for IDT for AWS IoT Greengrass\. 

1. On your computer, install and configure the AWS CLI if it's not already installed\. Follow the steps in [ Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.
**Note**  
The AWS CLI is an open source tool that you can use to interact with AWS services from your command\-line shell\.

1. Create the following customer managed policy that grants permissions to manage IDT and AWS IoT Greengrass roles\.

------
#### [ Linux or Unix ]

   ```
   aws iam create-policy --policy-name IDTUsageMetricsIAMPermissions --policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "iot-device-tester:SendMetrics"
               ],
               "Resource": "*"
           }
       ]
   }'
   ```

------
#### [ Windows command prompt ]

   ```
   aws iam create-policy --policy-name IDTUsageMetricsIAMPermissions --policy-document
                                           '{\"Version\": \"2012-10-17\", \"Statement\": [{\"Effect\": \"Allow\", \"Action\": [\"iot-device-tester:SendMetrics\"], \"Resource": \"*\"}]}'
   ```

**Note**  
This step includes a Windows command prompt example because it uses a different JSON syntax than Linux, macOS, or Unix terminal commands\.

------
#### [ PowerShell ]

   ```
   aws iam create-policy --policy-name IDTUsageMetricsIAMPermissions --policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "iot-device-tester:SendMetrics"
               ],
               "Resource": "*"
           }
       ]
   }'
   ```

------

1. Create an IAM user and attach the permissions required by IDT for AWS IoT Greengrass\.

   1. Create an IAM user\. 

      ```
      aws iam create-user --user-name user-name
      ```

   1. Attach the `IDTUsageMetricsIAMPermissions` policy you created to your IAM user\. Replace *user\-name* with your IAM user name and *<account\-id>* in the command with the ID of your AWS account\.

      ```
      aws iam attach-user-policy --user-name user-name --policy-arn arn:aws:iam::<account-id>:policy/IDTGreengrassIAMPermissions
      ```

1. Create a secret access key for the user\.

   ```
   aws iam create-access-key --user-name user-name
   ```

   Store the output in a secure location\. You use this information later to configure your AWS credentials file\.

## Provide AWS credentials to IDT<a name="idt-metrics-creds"></a>

To allow IDT to access your AWS credentials and submit metrics to AWS, do the following:

1. Store the AWS credentials for your IAM user as environment variables or in a credentials file:

   1. To use environment variables, run the following commands\.

------
#### [ Linux or Unix ]

      ```
      export AWS_ACCESS_KEY_ID=access-key
      export AWS_SECRET_ACCESS_KEY=secret-access-key
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      set AWS_ACCESS_KEY_ID=access-key
      set AWS_SECRET_ACCESS_KEY=secret-access-key
      ```

------
#### [ PowerShell ]

      ```
      $env:AWS_ACCESS_KEY_ID="access-key"
      $env:AWS_SECRET_ACCESS_KEY="secret-access-key"
      ```

------

   1. To use the credentials file, add the following information to the `~/.aws/credentials` file\.

      ```
      [profile-name]
      aws_access_key_id=access-key
      aws_secret_access_key=secret-access-key
      ```

1. Configure the `auth` section of the `config.json` file\. For more information, see [\(Optional\) Configure config\.json](set-custom-idt-config.md#config-json-custom)\.
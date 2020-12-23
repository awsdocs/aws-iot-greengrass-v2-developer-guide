# Getting started with AWS IoT Greengrass V2<a name="getting-started"></a>

You can complete this getting started tutorial to learn about the basic features of AWS IoT Greengrass V2\. In this tutorial, you do the following:
+ Install and configure the AWS IoT Greengrass V2 Core software on a Linux device, such as a Raspberry Pi\. This device is an AWS IoT Greengrass core device\.
+ Develop a Hello World component on your Greengrass core device\. Components are software modules that run on Greengrass core devices\.
+ Upload that component to AWS IoT Greengrass V2 in the AWS Cloud\.
+ Deploy that component to your Greengrass core device\.

## Prerequisites<a name="getting-started-prerequisites"></a>

To complete this getting started tutorial, you need the following:
+ An AWS account\. If you don't have one, see [Set up an AWS account](setting-up.md#set-up-aws-account)\.
+ An AWS Identity and Access Management \(IAM\) user with administrator permissions\.
+ A Windows, Mac, or Unix\-like development computer with an internet connection\.
+ A device with a Linux operating system and an internet connection to the same network as your development computer\. We recommend that you use a Raspberry Pi with [Raspberry Pi OS](https://www.raspberrypi.org/downloads/) \(previously called Raspbian\)\.
+ AWS CLI installed and configured with credentials on your development computer and on your device\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)\.
  + Minimum AWS CLI V1 version: v1\.18\.197
  + Minimum AWS CLI V2 version: v2\.1\.11
+ [Python](https://www.python.org/downloads/) 3\.5 or later installed on the device\.

## Set up your environment<a name="set-up-environment"></a>

Follow the steps in this section to set up a Linux device to use as your AWS IoT Greengrass core device\. These steps assume that you use a Raspberry Pi with Raspberry Pi OS\. If you use a different device or operating system, consult the relevant documentation for your device\.

**To set up a Raspberry Pi for AWS IoT Greengrass V2**

1. Enable SSH on your Raspberry Pi to remotely connect to it\. For more information, see [SSH \(Secure shell\)](https://www.raspberrypi.org/documentation/remote-access/ssh/) in the *Raspberry Pi Documentation*\.

1. Find the IP address of your Raspberry Pi to connect to it with SSH\. To do so, you can run the following command on your Raspberry Pi\.

   ```
   hostname -I
   ```

1. Connect to your Raspberry Pi with SSH\. On your development computer, run the following command\. Replace *username* with the name of the user to sign in, and replace *pi\-ip\-address* with the IP address that you found in the previous step\.

   ```
   ssh username@pi-ip-address
   ```

   The default Raspberry Pi user name and password are **pi** and **raspberry**, respectively\.
**Important**  
If your development computer uses an earlier version of Windows, you might not have the `ssh` command, or you might have `ssh` but can't connect to your Raspberry Pi\. To connect to your Raspberry Pi, you can install and configure [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), which is a no\-cost, open source SSH client\. Consult the [PuTTY documentation](https://tartarus.org/~simon/putty-snapshots/htmldoc/Chapter2.html#gs) to connect to your Raspberry Pi\.

1. Install the Java 8 runtime, which the AWS IoT Greengrass V2 Core software requires to run\. On your Raspberry Pi, run the following command\.

   ```
   sudo apt install openjdk-8-jdk
   ```

   When the installation completes, run the following command to verify that Java runs on your Raspberry Pi\.

   ```
   java -version
   ```

   The command output should be similar to the following\.

   ```
   openjdk version "1.8.0_242"
   OpenJDK Runtime Environment (build 1.8.0_242-b08)
   OpenJDK 64-Bit Server VM (build 25.242-b08, mixed mode)
   ```

## Install AWS IoT Greengrass V2<a name="install-greengrass-v2"></a>

Follow the steps in this section to install and configure the AWS IoT Greengrass V2 Core software on your Raspberry Pi\.

**To install and configure AWS IoT Greengrass V2**

1. On your AWS IoT Greengrass core device \(your Raspberry Pi\), run the following command to switch to the home directory\.

   ```
   cd ~
   ```

1. Run the following command to download the AWS IoT Greengrass Core software\.

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

   <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

1. Run the following command to unzip the software and remove the ZIP file\. Replace *GreengrassCore* with the name of the destination folder\.

   ```
   unzip greengrass-nucleus-latest.zip -d GreengrassCore && rm greengrass-nucleus-latest.zip
   ```

1. When you install the AWS IoT Greengrass Core software, you can configure options such as the root folder and AWS Region to use\. You can also choose to provision the AWS IoT Core thing that represents the AWS IoT Greengrass core device and choose to create the IAM role that AWS IoT Greengrass assumes to perform actions in the AWS Cloud\. The AWS IoT Greengrass Core software creates these resources only if they don't already exist\.

   The installer can also deploy the AWS IoT Greengrass CLI \(`greengrass-cli`\), which you can use to interact with the AWS IoT Greengrass Core software\. The Greengrass CLI includes commands to deploy and test custom components that you develop on the core device\.

   The core device uses your AWS credentials to provision these resources and create the deployment\. For more information, see [Minimal IAM policy to provision resources](install-greengrass-core-v2.md#provision-minimal-iam-policy)\.

   Do one of the following to retrieve credentials and provide them to the AWS IoT Greengrass Core software:<a name="installer-export-aws-credentials"></a>
   + \(Recommended\) Use temporary security credentials:

     1. Provide the access key ID, secret access key, and session token from an IAM role that you assume\. For more information about how to retrieve these credentials, see [Using temporary security credentials with the AWS CLI](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html#using-temp-creds-sdk-cli) in the *IAM User Guide*\.

     1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

        ```
        export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
        export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
        export AWS_SESSION_TOKEN=AQoDYXdzEJr1K...o5OytwEXAMPLE=
        ```
   + Use long\-term credentials:

     1. Provide the access key ID and secret access key for your IAM user\. For more information about how to retrieve long\-term credentials, see [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the *IAM User Guide*\.

     1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

        ```
        export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
        export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
        ```

   The AWS IoT Greengrass Core software doesn't save or store your credentials\.

1. Run the following command to launch the AWS IoT Greengrass Core software installer\. If your core device has [systemd](https://en.wikipedia.org/wiki/Systemd), this command installs the software as a system service that runs on boot\. This command also specifies to use the `ggc_user` system user and `ggc_group` system group to run software components on the core device\. The installer creates this default user and group for you\. Do the following:<a name="installer-replace-arguments"></a>

   1. Replace *region* with the AWS Region in which to find or create resources\.

   1. Replace *MyGreengrassCore* with the name of the AWS IoT thing for your Greengrass core device\. If the thing doesn't exist, the installer creates it\. The installer downloads the certificates to authenticate as the AWS IoT thing\. 

   1. Replace *MyGreengrassCoreGroup* with the name of AWS IoT thing group for your Greengrass core device\. If the thing group doesn't exist, the installer creates it and adds the thing to it\. If the thing group exists and has an active deployment, the core device downloads and runs the software which that deployment specifies\.

   1. Replace *MyGreengrassV2TokenExchangeRole* with the name of the IAM role that allows the Greengrass core device to get temporary AWS credentials\. If the role doesn't exist, the installer creates it and creates and attaches a policy named `MyGreengrassV2TokenExchangeRoleAccess`\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

   1. Replace *MyGreengrassCoreTokenExchangeRoleAlias* with the alias to the IAM role that allows the Greengrass core device to get temporary credentials later\. If the role alias doesn't exist, the installer creates it and points it to the IAM role that you specify\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

   1. Replace */greengrass/v2* with the path to the root folder to use to install the AWS IoT Greengrass Core software\.

   1. If you unpacked the installer to a different folder, replace *GreengrassCore* with the folder that you use\.

   ```
   sudo -E java -Dlog.store=FILE \
     -jar ./GreengrassCore/lib/Greengrass.jar \
     --aws-region region \
     --root /greengrass/v2 \
     --thing-name MyGreengrassCore \
     --thing-group-name MyGreengrassCoreGroup \
     --tes-role-name MyGreengrassV2TokenExchangeRole \
     --tes-role-alias-name MyGreengrassCoreTokenExchangeRoleAlias \
     --component-default-user ggc_user:ggc_group \
     --provision true \
     --setup-system-service true \
     --deploy-dev-tools true
   ```

   When you run this command, you should see the following messages to indicate that the installer succeeded\.

   ```
   Successfully configured Nucleus with provisioned resource details!
   Configured Nucleus to deploy aws.greengrass.Cli component
   Successfully set up Nucleus as a system service
   ```
**Note**  
If your system doesn't have systemd, the installer won't set up the software as a system service, and you won't see that success message\.

1. The local development tools can take up to a minute to deploy\. You can run the following command to check the status of the deployment\. Replace *MyGreengrassCore* with the name of your core device\.

   ```
   aws greengrassv2 list-effective-deployments --core-device-thing-name MyGreengrassCore
   ```

   The `coreDeviceExecutionStatus` indicates the status of the deployment to the core device\. When the status is `COMPLETED`, run the following command to verify that the Greengrass CLI is installed and runs\. Replace */greengrass/v2* with the path to the root folder\.

   ```
   /greengrass/v2/bin/greengrass-cli help
   ```

   The command outputs help information for the Greengrass CLI\. If the `greengrass-cli` isn't found, the deployment might have failed to install the Greengrass CLI\. For more information, see [Troubleshooting](troubleshooting.md)\.
**Tip**  
You can add the Greengrass CLI to your `PATH` environment variable to run `greengrass-cli` without its absolute path\.

1. If you installed the software as a system service, the installer runs the software for you\. Otherwise, you must run the software\. To see if the installer set up the software as a system service, look for the following line in the installer output\.

   ```
   Successfully set up Nucleus as a system service
   ```

   If you don't see this message, do the following to run the software:

   1. Run the following command to run the software\.

      ```
      sudo /greengrass/v2/alts/current/distro/bin/loader
      ```

      The software prints the following message if it launches successfully\.

      ```
      Launched Nucleus successfully.
      ```

   1. You must leave the current terminal session open to keep the AWS IoT Greengrass Core software running\. On your development computer, run the following command to open a second SSH session that you can use to run additional commands on the core device\. Replace *username* with the name of the user to sign in, and replace *pi\-ip\-address* with the IP address of the device\.

      ```
      ssh username@pi-ip-address
      ```
**Note**  
If the software runs as a system service, you can start and stop it with the following commands\.  
To start the software as a service, run the following command\.  

     ```
     sudo service greengrass start
     ```
To stop the software as a service, run the following command\.  

     ```
     sudo service greengrass stop
     ```

The AWS IoT Greengrass Core software and local development tools run on your device\. Next, you can develop a Hello World AWS IoT Greengrass component on your device\.

## Create your first component<a name="create-first-component"></a>

Components are software that run on the AWS IoT Greengrass core device\. Every component is composed of a *recipe* and *artifacts*\.
+ <a name="component-recipe-definition"></a>**Recipes**

  Every component contains a recipe file, which defines its metadata\. The recipe also specifies the component's parameters, dependencies, lifecycle, and platform compatibility\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.
+ <a name="component-artifacts-definition"></a>**Artifacts**

  Components can have any number of artifacts, which are component binaries\. Artifacts can include scripts, compiled code, static resources, and anything else that a component consumes\. Components can also consume artifacts from component dependencies\.

With AWS IoT Greengrass, you can develop and test components on your Greengrass core device without interaction with the AWS Cloud\. When you complete your component, you can upload it to AWS IoT Greengrass to deploy it to other devices\.

In this section, you learn how to create and run a basic Hello World component\.

**To create a Hello World component**

1. On your Greengrass core device, run the following command to change to the AWS IoT Greengrass installation folder to use for local development\. Replace *\~/GreengrassCore* with the path to the installation folder\.

   ```
   cd ~/GreengrassCore
   ```

1. AWS IoT Greengrass expects that component recipes and artifacts are in separate folders\.

   Run the following command to create a folder for the component recipe\.

   ```
   mkdir recipes
   ```

1. Run the following command to create the recipe file and open it in a text editor\.

   ```
   nano recipes/com.example.HelloWorld-1.0.0.yaml
   ```

   Paste the following recipe into the file\.

   ```
   ---
   RecipeFormatVersion: 2020-01-25
   ComponentName: com.example.HelloWorld
   ComponentVersion: '1.0.0'
   ComponentDescription: My first AWS IoT Greengrass component.
   ComponentPublisher: Amazon
   ComponentConfiguration:
     DefaultConfiguration:
       Message: world
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Run: |
           python3 {artifacts:path}/hello_world.py '{configuration:/Message}'
   ```

   This recipe's `ComponentConfiguration` section defines a parameter, `Message`, that defaults to `world`\. The `Lifecycle` section instructs the AWS IoT Greengrass core to run the Hello World script with the `Message` parameter value as an argument\.

1. Run the following command to create a folder for the component artifacts\.

   ```
   mkdir -p artifacts/com.example.HelloWorld/1.0.0
   ```
**Important**  <a name="local-artifact-folder-name-requirements"></a>
You must use the following format for the artifact folder path\. Include the component name and version that you specify in the recipe\.  

   ```
   artifacts/componentName/componentVersion/
   ```

1. Run the following command to create a Python script artifact file and open it in a text editor\.

   ```
   nano artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

   Paste the following Python script into the file\.

   ```
   import sys
   import datetime
   
   message = f"Hello, {sys.argv[1]}! Current time: {str(datetime.datetime.now())}."
   
   # Print the message to stdout.
   print(message)
   
   # Append the message to the log file.
   with open('/tmp/Greengrass_HelloWorld.log', 'a') as f:
       print(message, file=f)
   ```

   This Python script logs a hello message and the current time to `/tmp/Greengrass_HelloWorld.log`\.

1. AWS IoT Greengrass provides a command line interface \(CLI\) that you can use to manage components on your AWS IoT Greengrass core device\.

   Run the following command to deploy the component to the AWS IoT Greengrass core\. Replace */greengrass/v2* with your AWS IoT Greengrass V2 root folder, and replace *\~/GreengrassCore* with your AWS IoT Greengrass V2 installation folder\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create \
     --recipeDir ~/GreengrassCore/recipes \
     --artifactDir ~/GreengrassCore/artifacts \
     --merge "com.example.HelloWorld=1.0.0"
   ```

   This command adds the component that uses the recipe in `recipes` and the Python script in `artifacts`\. The `--merge` option adds or updates the component and version that you specify\.

1. Run the following command to verify that the Hello World component runs and logs messages\.

   ```
   tail -f /tmp/Greengrass_HelloWorld.log
   ```

   You should see messages similar to the following example\.

   ```
   Hello, world! Current time: 2020-11-30 17:48:59.153933.
   ```
**Note**  
If the `tail` command tells you that the file doesn't exist, the local deployment may not be complete yet\. If the file doesn't exist within 15 seconds, the deployment likely failed\. This can occur if your recipe isn't valid, for example\. Run the following command to view the AWS IoT Greengrass core log file\. This file includes logs from the Greengrass core device's deployment service\.  

   ```
   sudo tail -f /greengrass/v2/logs/greengrass.log
   ```
You can also view the log file for your Hello World component\.  

   ```
   sudo tail -f /greengrass/v2/logs/com.example.HelloWorld.log
   ```
For more information, see [Troubleshooting](troubleshooting.md)\.

1. You can modify local components to iterate and test your code\.

   Run the following command to edit `hello_world.py`\.

   ```
   nano artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

1. Add the following code at line 5 to edit the message that the AWS IoT Greengrass core logs\.

   ```
   message += " Greetings from your first Greengrass component."
   ```

   The `hello_world.py` script should now have the following contents\.

   ```
   import sys
   import datetime
   
   message = f"Hello, {sys.argv[1]}! Current time: {str(datetime.datetime.now())}."
   message += " Greetings from your first Greengrass component."
   # Print the message to stdout.
   print(message)
   
   # Append the message to the log file.
   with open('/tmp/Greengrass_HelloWorld.log', 'a') as f:
       print(message, file=f)
   ```

1. Run the following command to update the component with your changes\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create \
     --recipeDir ~/GreengrassCore/recipes \
     --artifactDir ~/GreengrassCore/artifacts \
     --merge "com.example.HelloWorld=1.0.0"
   ```

   This command applies the latest Hello World artifact to the AWS IoT Greengrass core runtime\.

1. Run the following command to restart the component\. When you restart a component, the AWS IoT Greengrass Core uses the latest changes\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli component restart \
     --names "com.example.HelloWorld"
   ```

1. Check the log again to verify that the Hello World component outputs the new message\.

   ```
   tail -f /tmp/Greengrass_HelloWorld.log
   ```

   You should see messages similar to the following example\.

   ```
   Hello, world! Current time: 2020-11-30 17:48:59.153933. Greetings from your first Greengrass component.
   ```

1. After you finish your component, remove it from your core device\. This is required for you to deploy the component back to the core device after you upload it to AWS IoT Greengrass\. Otherwises, the deployment fails with a version compatibility error because the local deployment specifies a different version\. Run the following command\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create --remove="com.example.HelloWorld"
   ```

Your Hello World component is complete, and you can now upload it to the AWS IoT Greengrass service\. Then, you can deploy the component to AWS IoT Greengrass core devices\.

## Upload your component<a name="upload-first-component"></a>

When you finish a component, you can upload it to the AWS Cloud\. AWS IoT Greengrass provides a component management service that hosts your components so that you can deploy them to devices or fleets of devices\. To upload a component to AWS IoT Greengrass, you complete the following steps:
+ Upload component artifacts to an Amazon S3 bucket\.
+ Add each artifact's Amazon S3 URI to the component recipe\.
+ Create a component in AWS IoT Greengrass from the component recipe\.

In this section, you complete these steps on your AWS IoT Greengrass core to upload your Hello World component to AWS IoT Greengrass\.

**To upload your Hello World component**

1. Create an Amazon S3 bucket to host your AWS IoT Greengrass component artifacts\. Run the following command to create a bucket with your AWS account ID and AWS Region to use a unique bucket name\. Replace *123456789012* with your AWS account ID and *region* with the AWS Region that you use for this tutorial\.

   ```
   aws s3 mb s3://greengrass-component-artifacts-123456789012-region
   ```

   The command outputs the following information if the request succeeds\.

   ```
   make_bucket: greengrass-component-artifacts-123456789012-region
   ```

1. Allow the core device to access component artifacts in this Amazon S3 bucket\. Each core device has a [core device IAM role](device-service-role.md) that allows it to interact with AWS IoT and send logs to the AWS Cloud\. This device role doesn't allow access to Amazon S3 buckets by default, so you must create and attach a policy that allows the core device to retrieve component artifacts from the S3 bucket\. Do the following:

   1. Create a file called `component-artifact-policy.json` and copy the following JSON into the file\. This policy allows access to all files in the Amazon S3 bucket that you created in the previous step\. Replace *123456789012* with your AWS account ID and *region* with the AWS Region in the bucket name\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::greengrass-component-artifacts-123456789012-region/*"
          }
        ]
      }
      ```

   1. Run the following command to create the policy from the policy document in `component-artifact-policy.json`\.

      ```
      aws iam create-policy \
        --policy-name MyGreengrassV2ComponentArtifactPolicy \
        --policy-document file://component-artifact-policy.json
      ```

      Copy the policy ARN from the policy metadata in the output\. You use this ARN to attach this policy to the core device role in the next step\.

   1. Run the following command to attach the policy to the core device role\. Replace *MyGreengrassV2TokenExchangeRole* with the name of the role that you specified when you ran the AWS IoT Greengrass Core software, and replace the policy ARN with the ARN from the previous step\.

      ```
      aws iam attach-role-policy \
        --role-name MyGreengrassV2TokenExchangeRole \
        --policy-arn arn:aws:iam::123456789012:policy/MyGreengrassV2ComponentArtifactPolicy
      ```

      If the command has no output, it succeeded, and your core device can access artifacts that you upload to this Amazon S3 bucket\.

1. Upload the Hello World Python script artifact to the Amazon S3 bucket\. Run the following command to upload the script to the same path in the bucket as the script exists at on your AWS IoT Greengrass core\.

   ```
   aws s3 cp \
     artifacts/com.example.HelloWorld/1.0.0/hello_world.py \
     s3://greengrass-component-artifacts-123456789012-region/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

   The command outputs a line that starts with `upload:` if the request succeeds\.

1. Add the artifact's Amazon S3 URI to the component recipe\. The Amazon S3 URI is composed of the bucket name and the path to the artifact object in the bucket\. Your script artifact's Amazon S3 URI is the URI that you upload the artifact to in the previous step\. This URI should look similar to the following example\.

   ```
   s3://greengrass-component-artifacts-123456789012-region/artifacts/HelloWorld/1.0.0/hello_world.py
   ```

   To add the artifact to the recipe, add a list of `Artifacts` that contains a structure with the Amazon S3 URI\.

   ```
   Artifacts:
     - URI: s3://greengrass-component-artifacts-123456789012-region/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

   Run the following command to open the recipe file\.

   ```
   nano recipes/com.example.HelloWorld-1.0.0.yaml
   ```

   Add the artifact to the recipe\. Your recipe file should look similar to the following example\.

   ```
   ---
   RecipeFormatVersion: 2020-01-25
   ComponentName: com.example.HelloWorld
   ComponentVersion: '1.0.0'
   ComponentDescription: My first AWS IoT Greengrass component.
   ComponentPublisher: Amazon
   ComponentConfiguration:
     DefaultConfiguration:
       Message: world
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Run: |
           python3 {artifacts:path}/hello_world.py '{configuration:/Message}'
       Artifacts:
         - URI: s3://greengrass-component-artifacts-123456789012-region/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

1. Create a component resource in AWS IoT Greengrass from the recipe\. Run the following command to create the component from the recipe, which you provide as a binary file\.

   ```
   aws greengrassv2 create-component-version \
     --inline-recipe fileb://recipes/com.example.HelloWorld-1.0.0.yaml
   ```

   The response looks similar to the following example if the request succeeds\.

   ```
   {
     "arn": "arn:aws:greengrass:region:123456789012:components:com.example.HelloWorld:versions:1.0.0",
     "componentName": "com.example.HelloWorld",
     "componentVersion": "1.0.0",
     "creationTimestamp": "Mon Nov 30 09:04:05 UTC 2020",
     "status": {
       "componentState": "REQUESTED",
       "message": "NONE",
       "errors": {}
     }
   }
   ```

   Copy the `arn` from the output to check the state of the component in the next step\.
**Note**  
You can also see your Hello World component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) on the **Components** page\.

1. When you create a component, its state is `REQUESTED`\. Then, AWS IoT Greengrass validates that the component is deployable\. You can run the following command to query the component status and verify that your component is deployable\. Replace the `arn` with the ARN from the previous step\.

   ```
   aws greengrassv2 describe-component \
     --arn "arn:aws:greengrass:region:123456789012:components:com.example.HelloWorld:versions:1.0.0"
   ```

   If the component validates, the response indicates that the component state is `DEPLOYABLE`\.

   ```
   {
     "arn": "arn:aws:greengrass:region:123456789012:components:com.example.HelloWorld:versions:1.0.0",
     "componentName": "com.example.HelloWorld",
     "componentVersion": "1.0.0",
     "creationTimestamp": "2020-11-30T18:04:05.823Z",
     "publisher": "Amazon",
     "description": "My first AWS IoT Greengrass component.",
     "status": {
       "componentState": "DEPLOYABLE",
       "message": "NONE",
       "errors": {}
     },
     "platforms": [
       {
         "os": "linux",
         "architecture": "all"
       }
     ]
   }
   ```

Your Hello World component is now available in AWS IoT Greengrass and you can deploy it to other AWS IoT Greengrass core devices\.

## Deploy your component<a name="deploy-first-component"></a>

With AWS IoT Greengrass, you can deploy components to individual devices or groups of devices\. You specify which components to deploy and the configuration update to deploy for each component\. You can also control how the deployment rolls out to the devices that you choose to receive the update\.

In this section, you deploy your Hello World component back to your AWS IoT Greengrass core device\.

**To deploy your Hello World component**

1. On your development computer, create a file called `hello-world-deployment.json` and copy the following JSON into the file\. This file defines the components and configurations to deploy\.

   ```
   {
     "components": {
       "com.example.HelloWorld": {
         "componentVersion": "1.0.0",
         "configurationUpdate": {
           "merge": "{\"Message\":\"universe\"}"
         }
       }
     }
   }
   ```

   This configuration file specifies to deploy version `1.0.0` of the hello world component that you developed and published in the previous procedure\. The `configurationUpdate` specifies to merge the component configuration in a JSON\-encoded string\. This configuration update sets the Hello World `Message` parameter to `universe` for the device in this deployment\.

1. You deploy to things \(individual devices\) or thing groups \(groups of devices\)\. Run the following command to create a deployment for your AWS IoT Greengrass core device\. Replace *MyGreengrassCoreGroup* with the name of the AWS IoT thing group for your AWS IoT Greengrass core device\.

   ```
   aws greengrassv2 create-deployment \
     --target-arn "arn:aws:iot:region:account-id:thinggroup/MyGreengrassCoreGroup"
     --cli-input-json file://hello-world-deployment.json
   ```

   The command outputs a response similar to the following example\.

   ```
   {
     "deploymentId": "deb69c37-314a-4369-a6a1-3dff9fce73a9",
     "iotJobId": "b5d92151-6348-4941-8603-bdbfb3e02b75",
     "iotJobArn": "arn:aws:iot:region:account-id:job/b5d92151-6348-4941-8603-bdbfb3e02b75"
   }
   ```

1. The deployment can take several minutes to complete\. Check the Hello World log to verify the change\. Run the following command on your AWS IoT Greengrass core device\.

   ```
   tail -f /tmp/Greengrass_HelloWorld.log
   ```

   You should see messages similar to the following example\.

   ```
   Hello, universe! Current time: 2020-11-30 18:18:59.153933. Greetings from your first Greengrass component.
   ```
**Note**  
If the log messages don't change, the deployment failed or didn't reach the core device\. This can occur if your core device isn't connected to the internet or doesn't have permissions to retrieve artifacts from your S3 bucket\. Run the following command on your core device to view the AWS IoT Greengrass core log file\. This file includes logs from the Greengrass core device's deployment service\.  

   ```
   sudo tail -f /greengrass/v2/logs/greengrass.log
   ```
For more information, see [Troubleshooting](troubleshooting.md)\.

You've completed this tutorial\. The AWS IoT Greengrass Core software and your Hello World component run on your device\. Also, your Hello World component is available in the AWS Cloud to deploy to other devices\. For more information about the topics that this tutorial explores, see the following:
+ [Create custom AWS IoT Greengrass components](create-components.md)
+ [Upload components to deploy to your core devicesUpload components to deploy](upload-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
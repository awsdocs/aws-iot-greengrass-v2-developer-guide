# Getting started with AWS IoT Greengrass V2<a name="getting-started"></a>

You can complete this getting started tutorial to learn about the basic features of AWS IoT Greengrass V2\. In this tutorial, you do the following:
+ Install and configure the AWS IoT Greengrass Core software on a Linux device, such as a Raspberry Pi\. This device is an AWS IoT Greengrass core device\.
+ Develop a Hello World component on your Greengrass core device\. Components are software modules that run on Greengrass core devices\.
+ Upload that component to AWS IoT Greengrass V2 in the AWS Cloud\.
+ Deploy that component from the AWS Cloud to your Greengrass core device\.

**Note**  
This tutorial describes how to set up a development environment and explore the features of AWS IoT Greengrass\. For more information about how to set up and configure production devices, see the following:  
[Setting up AWS IoT Greengrass core devices](setting-up.md)
[Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)

You can expect to spend 20\-30 minutes on this tutorial\.

## Prerequisites<a name="getting-started-prerequisites"></a>

To complete this getting started tutorial, you need the following:
+ An AWS account\. If you don't have one, see [Set up an AWS account](setting-up.md#set-up-aws-account)\.
+ <a name="requirement-supported-region"></a>The use of an [AWS Region](https://en.wikipedia.org/wiki/Amazon_Web_Services#Availability_and_topology) that supports AWS IoT Greengrass V2\. For the list of supported Regions, see [AWS IoT Greengrass V2 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/greengrassv2.html) in the *AWS General Reference*
+ An AWS Identity and Access Management \(IAM\) user with administrator permissions\.
+ A Windows, macOS, or Unix\-like development computer with an internet connection\.
+ A device that meets the [Requirements to install and run the AWS IoT Greengrass Core software v2\.0](setting-up.md#greengrass-v2-requirements) and has an internet connection to the same network as your development computer\. We recommend that you use a Raspberry Pi with [Raspberry Pi OS](https://www.raspberrypi.org/downloads/) \(previously called Raspbian\)\.
+ [Python](https://www.python.org/downloads/) 3\.5 or later installed on the device\.
+ AWS Command Line Interface \(AWS CLI\) installed and configured with credentials on your development computer and on your device\. Make sure you use the same AWS Region to configure the AWS CLI on your development computer and on your device\. To use AWS IoT Greengrass V2 with the AWS CLI, you must have one of the following versions or later:<a name="minimum-aws-cli-versions"></a>
  + Minimum AWS CLI V1 version: v1\.18\.197
  + Minimum AWS CLI V2 version: v2\.1\.11
**Tip**  <a name="tip-check-aws-cli-version"></a>
You can run the following command to check the version of the AWS CLI that you have\.  

  ```
  aws --version
  ```

  For more information, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS Command Line Interface User Guide*\.
**Note**  
If you use a Raspberry Pi or another 32\-bit ARM device, install AWS CLI V1\. AWS CLI V2 isn't available for 32\-bit ARM devices\. For more information, see [Installing, updating, and uninstalling the AWS CLI version 1](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv1.html)\.

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

1. Install the Java runtime, which AWS IoT Greengrass Core software requires to run\. On your Raspberry Pi, use the following commands to install Java 11\.

   ```
   sudo apt install default-jdk
   ```

   When the installation completes, run the following command to verify that Java runs on your Raspberry Pi\.

   ```
   java -version
   ```

   The command prints the version of Java that runs on the device\. The output might look similar to the following example\.

   ```
   openjdk version "11.0.9.1" 2020-11-04
   OpenJDK Runtime Environment (build 11.0.9.1+1-post-Debian-1deb10u2)
   OpenJDK 64-Bit Server VM (build 11.0.9.1+1-post-Debian-1deb10u2, mixed mode)
   ```

## Install the AWS IoT Greengrass Core software<a name="install-greengrass-v2"></a>

Follow the steps in this section to set up your Raspberry Pi as a AWS IoT Greengrass core device that you can use for local development\. In this section, you download and run an installer that does the following to configure the AWS IoT Greengrass Core software for your device:
+ Installs the Greengrass nucleus component, which is the only mandatory component and the minimum requirement to run the AWS IoT Greengrass Core software on a device\. For more information, see [Greengrass nucleus component](greengrass-nucleus-component.md)\.
+ Registers your device as an AWS IoT thing and downloads a digital certificate that allows your device to connect to AWS\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.
+ Adds the device's AWS IoT thing to a thing group, which is a group or fleet of AWS IoT things\. Thing groups enable you to manage fleets of Greengrass core devices\. When you deploy software components to your devices, you can choose to deploy to individual devices or to groups of devices\. For more information, see [Managing devices with AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/iot-thing-management.html) in the *AWS IoT Core Developer Guide*\.
+ Creates the IAM role that allows your Greengrass core device to interact with AWS services\. By default, this role allows your device to interact with AWS IoT and send logs to Amazon CloudWatch Logs\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.
+ Installs the AWS IoT Greengrass command line interface \(`greengrass-cli`\), which you can use to test custom components that you develop on the core device\. For more information, see [Greengrass Command Line Interface](gg-cli.md)\.

**To install and configure the AWS IoT Greengrass Core software**

1. On your AWS IoT Greengrass core device \(your Raspberry Pi\), run the following command to switch to the home directory\.

   ```
   cd ~
   ```

1. Run the following command to download the AWS IoT Greengrass Core software\.

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

   <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

1. Run the following command to unzip the software and remove the ZIP file\. Replace *GreengrassInstaller* with the name of the destination folder\.

   ```
   unzip greengrass-nucleus-latest.zip -d GreengrassInstaller && rm greengrass-nucleus-latest.zip
   ```

1. Provide your AWS credentials so that the installer can provision the AWS IoT and IAM resources for your core device\. To increase security, you can get credentials for an IAM role that allows only the minimum permissions necessary to provision\. For more information, see [Minimal IAM policy for installer to provision resources](provision-minimal-iam-policy.md)\.

   Do one of the following to retrieve credentials and provide them to the AWS IoT Greengrass Core software:<a name="installer-export-aws-credentials"></a>
   + Use long\-term credentials from an IAM user:

     1. Provide the access key ID and secret access key for your IAM user\. For more information about how to retrieve long\-term credentials, see [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the *IAM User Guide*\.

     1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

        ```
        export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
        export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
        ```
   + \(Recommended\) Use temporary security credentials from an IAM role:

     1. Provide the access key ID, secret access key, and session token from an IAM role that you assume\. For more information about how to retrieve these credentials, see [Using temporary security credentials with the AWS CLI](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html#using-temp-creds-sdk-cli) in the *IAM User Guide*\.

     1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

        ```
        export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
        export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
        export AWS_SESSION_TOKEN=AQoDYXdzEJr1K...o5OytwEXAMPLE=
        ```

   The AWS IoT Greengrass Core software doesn't save or store your credentials\.

1. Run the following command to launch the AWS IoT Greengrass Core software installer\. If your core device has [systemd](https://en.wikipedia.org/wiki/Systemd), this command installs the software as a system service that runs on boot\. This command also specifies to use the `ggc_user` system user and `ggc_group` system group to run software components on the core device\. The installer creates this default user and group for you\. Replace argument values in your command as follows\.<a name="installer-replace-arguments"></a>

   1. */greengrass/v2*: The path to the root folder to use to install the AWS IoT Greengrass Core software\.

   1. *GreengrassInstaller*\. The path to the folder where you unpacked the AWS IoT Greengrass Core software installer\.

   1. *region*\. The AWS Region in which to find or create resources\.

   1. *MyGreengrassCore*\. The name of the AWS IoT thing for your Greengrass core device\. If the thing doesn't exist, the installer creates it\. The installer downloads the certificates to authenticate as the AWS IoT thing\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.
**Note**  <a name="install-argument-thing-name-constraint"></a>
The thing name can't contain colon \(`:`\) characters\.

   1. *MyGreengrassCoreGroup*\. The name of AWS IoT thing group for your Greengrass core device\. If the thing group doesn't exist, the installer creates it and adds the thing to it\. If the thing group exists and has an active deployment, the core device downloads and runs the software that the deployment specifies\.
**Note**  <a name="install-argument-thing-group-name-constraint"></a>
The thing group name can't contain colon \(`:`\) characters\.

   1. *GreengrassV2IoTThingPolicy*\. The name of the AWS IoT policy that allows the Greengrass core devices to communicate with AWS IoT and AWS IoT Greengrass\. If the AWS IoT policy doesn't exist, the installer creates a permissive AWS IoT policy with this name\. You can restrict this policy's permissions for you use case\. For more information, see [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy)\.

   1. *GreengrassV2TokenExchangeRole*\. The name of the IAM role that allows the Greengrass core device to get temporary AWS credentials\. If the role doesn't exist, the installer creates it and creates and attaches a policy named `GreengrassV2TokenExchangeRoleAccess`\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

   1. *GreengrassCoreTokenExchangeRoleAlias*\. The alias to the IAM role that allows the Greengrass core device to get temporary credentials later\. If the role alias doesn't exist, the installer creates it and points it to the IAM role that you specify\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

   ```
   sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE \
     -jar ./GreengrassInstaller/lib/Greengrass.jar \
     --aws-region region \
     --thing-name MyGreengrassCore \
     --thing-group-name MyGreengrassCoreGroup \
     --thing-policy-name GreengrassV2IoTThingPolicy \
     --tes-role-name GreengrassV2TokenExchangeRole \
     --tes-role-alias-name GreengrassCoreTokenExchangeRoleAlias \
     --component-default-user ggc_user:ggc_group \
     --provision true \
     --setup-system-service true \
     --deploy-dev-tools true
   ```
**Note**  
<a name="jvm-tuning-note"></a>If you are running AWS IoT Greengrass on a device with limited memory, you can control the amount of memory that AWS IoT Greengrass Core software uses\. To control memory allocation, you can set JVM heap size options in the `jvmOptions` configuration parameter in your nucleus component\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

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

   The `coreDeviceExecutionStatus` indicates the status of the deployment to the core device\. When the status is `SUCCEEDED`, run the following command to verify that the Greengrass CLI is installed and runs\. Replace */greengrass/v2* with the path to the root folder\.

   ```
   /greengrass/v2/bin/greengrass-cli help
   ```

   The command outputs help information for the Greengrass CLI\. If the `greengrass-cli` isn't found, the deployment might have failed to install the Greengrass CLI\. For more information, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\.

   You can also run the following command to manually deploy the AWS IoT Greengrass CLI to your device\.
   + Replace *region* with the AWS Region that you use\. Make sure that you use the same AWS Region that you used to configure the AWS CLI on your device\.
   + Replace *account\-id* with your AWS account ID\.
   + Replace *MyGreengrassCore* with the name of your core device\.

------
#### [ Linux, macOS, or Unix ]

   ```
   aws greengrassv2 create-deployment \
     --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" \
     --components '{
       "aws.greengrass.Cli": {
         "componentVersion": "2.4.0"
       }
     }'
   ```

------
#### [ Windows \(CMD\) ]

   ```
   aws greengrassv2 create-deployment ^
     --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" ^
     --components "{\"aws.greengrass.Cli\":{\"componentVersion\":\"2.4.0\"}}"
   ```

------
#### [ Windows \(PowerShell\) ]

   ```
   aws greengrassv2 create-deployment `
     --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" `
     --components '{\"aws.greengrass.Cli\":{\"componentVersion\":\"2.4.0\"}}'
   ```

------
**Tip**  
You can add `/greengrass/v2/bin` to your `PATH` environment variable to run `greengrass-cli` without its absolute path\.

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

   For more information about how to interact with the Greengrass system service, see [Configure AWS IoT Greengrass as a system service](configure-greengrass-core-v2.md#configure-system-service)\.

The AWS IoT Greengrass Core software and local development tools run on your device\. Next, you can develop a Hello World AWS IoT Greengrass component on your device\.

## Create your first component<a name="create-first-component"></a>

A component is a software module that runs on AWS IoT Greengrass core devices\. Components enable you to create and manage complex applications as discrete building blocks that you can reuse from one Greengrass core device to another\. Every component is composed of a *recipe* and *artifacts*\.
+ <a name="component-recipe-definition"></a>**Recipes**

  Every component contains a recipe file, which defines its metadata\. The recipe also specifies the component's configuration parameters, component dependencies, lifecycle, and platform compatibility\. The component lifecycle defines the commands that install, run, and shut down the component\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.

  You can define recipes in [JSON](https://en.wikipedia.org/wiki/JSON) or [YAML](https://en.wikipedia.org/wiki/YAML) format\.
+ <a name="component-artifacts-definition"></a>**Artifacts**

  Components can have any number of artifacts, which are component binaries\. Artifacts can include scripts, compiled code, static resources, and any other files that a component consumes\. Components can also consume artifacts from component dependencies\.

With AWS IoT Greengrass, you can develop and test components on your Greengrass core device without interaction with the AWS Cloud\. When you complete your component, you can upload it to AWS IoT Greengrass to deploy it to other devices\. For more information, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md)\.

In this section, you learn how to create and run a basic Hello World component\.

**To create a Hello World component**

1. On your Greengrass core device, run the following command to change to the AWS IoT Greengrass installation folder to use for local development\. Replace *\~/GreengrassInstaller* with the path to the installation folder\.

   ```
   cd ~/GreengrassInstaller
   ```

1. AWS IoT Greengrass expects that component recipes and artifacts are in separate folders\.

   Run the following command to create a folder for the component recipe\.

   ```
   mkdir recipes
   ```

1. Run the following command to create the recipe file and open it in a text editor\.

------
#### [ JSON ]

   ```
   nano recipes/com.example.HelloWorld-1.0.0.json
   ```

------
#### [ YAML ]

   ```
   nano recipes/com.example.HelloWorld-1.0.0.yaml
   ```

------

   Paste the following recipe into the file\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.HelloWorld",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "My first AWS IoT Greengrass component.",
     "ComponentPublisher": "Amazon",
     "ComponentConfiguration": {
       "DefaultConfiguration": {
         "Message": "world"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Run": "python3 -u {artifacts:path}/hello_world.py '{configuration:/Message}'"
         }
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
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
           python3 -u {artifacts:path}/hello_world.py '{configuration:/Message}'
   ```

------

   This recipe's `ComponentConfiguration` section defines a parameter, `Message`, that defaults to `world`\. The `Manifests` section defines a *manifest*, which is a set of lifecycle instructions and artifacts for a platform\. You can define multiple manifests to specify different install instructions for various platforms, for example\. In the manifest, the `Lifecycle` section instructs the Greengrass core device to run the Hello World script with the `Message` parameter value as an argument\.

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
   
   message = "Hello, %s! Current time: %s." % (sys.argv[1], datetime.datetime.now())
   
   # Print the message to stdout.
   print(message)
   
   # Append the message to the log file.
   with open('/tmp/Greengrass_HelloWorld.log', 'a') as f:
       print(message, file=f)
   ```

   This Python script logs a hello message and the current time to `/tmp/Greengrass_HelloWorld.log`\.

1. Use the local AWS IoT Greengrass CLI to manage components on your Greengrass core device\.

   Run the following command to deploy the component to the AWS IoT Greengrass core\. Replace */greengrass/v2* with your AWS IoT Greengrass V2 root folder, and replace *\~/GreengrassInstaller* with your AWS IoT Greengrass V2 installation folder\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create \
     --recipeDir ~/GreengrassInstaller/recipes \
     --artifactDir ~/GreengrassInstaller/artifacts \
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
For more information, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\.

1. Modify the local component to iterate and test your code\.

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
   
   message = "Hello, %s! Current time: %s." % (sys.argv[1], datetime.datetime.now())
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
     --recipeDir ~/GreengrassInstaller/recipes \
     --artifactDir ~/GreengrassInstaller/artifacts \
     --merge "com.example.HelloWorld=1.0.0"
   ```

   This command applies the latest Hello World artifact to the AWS IoT Greengrass core runtime\.

1. Run the following command to restart the component\. When you restart a component, the core device uses the latest changes\.

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

1. You can update the component's configuration parameters to test different configurations\. When you deploy a component, you can specify a *configuration update*, which defines how to modify the component's configuration on the core device\. You can specify which configuration values to reset to default values and the new configuration values to merge onto the core device\. For more information, see [Update component configurations](update-component-configurations.md)\.

   Do the following:

   1. Create a file called `hello-world-config-update.json` to contain the configuration update\.

      ```
      nano hello-world-config-update.json
      ```

   1. Copy the following JSON object into the file\. This JSON object defines a configuration update that merges the value `friend` to the `Message` parameter to update its value\. This configuration update doesn't specify any values to reset\. You don't need to reset the `Message` parameter because the merge update replaces the existing value\.

      ```
      {
        "com.example.HelloWorld": {
          "MERGE": {
            "Message": "friend"
          }
        }
      }
      ```

   1. Run the following command to deploy the configuration update to the Hello World component\.

      ```
      sudo /greengrass/v2/bin/greengrass-cli deployment create \
        --merge "com.example.HelloWorld=1.0.0" \
        --update-config hello-world-config-update.json
      ```

   1. Check the log again to verify that the Hello World component outputs the new message\.

      ```
      tail -f /tmp/Greengrass_HelloWorld.log
      ```

      You should see messages similar to the following example\.

      ```
      Hello, friend! Current time: 2020-11-30 17:48:59.153933. Greetings from your first Greengrass component.
      ```

1. After you finish testing your component, remove it from your core device\. Run the following command\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create --remove="com.example.HelloWorld"
   ```
**Important**  
This step is required for you to deploy the component back to the core device after you upload it to AWS IoT Greengrass\. Otherwise, the deployment fails with a version compatibility error because the local deployment specifies a different version of the component\.

   Run the following command and verify that the `com.example.HelloWorld` component doesn't appear in the list of components on your device\. 

   ```
   sudo /greengrass/v2/bin/greengrass-cli component list
   ```

Your Hello World component is complete, and you can now upload it to the AWS IoT Greengrass service\. Then, you can deploy the component to AWS IoT Greengrass core devices\.

## Upload your component<a name="upload-first-component"></a>

When you finish a component, you can upload it to the AWS IoT Greengrass service in the AWS Cloud\. AWS IoT Greengrass provides a component management service that hosts your components so that you can deploy them to individual devices or fleets of devices\. To upload a component to AWS IoT Greengrass, you complete the following steps:
+ Upload component artifacts to an S3 bucket\.
+ Add each artifact's Amazon Simple Storage Service \(Amazon S3\) URI to the component recipe\.
+ Create a component in AWS IoT Greengrass from the component recipe\.

In this section, you complete these steps on your AWS IoT Greengrass core device to upload your Hello World component to AWS IoT Greengrass\.

**To upload your Hello World component**

1. Use an S3 bucket in your AWS account to host AWS IoT Greengrass component artifacts\. When you deploy the component to a core device, the device downloads the component's artifacts from the bucket\.

   You can use an existing S3 bucket, or run the following command to create a bucket\. This command creates a bucket with your AWS account ID and AWS Region to form a unique bucket name\. Replace *123456789012* with your AWS account ID and *region* with the AWS Region that you use for this tutorial\.

   ```
   aws s3 mb s3://greengrass-component-artifacts-123456789012-region
   ```

   The command outputs the following information if the request succeeds\.

   ```
   make_bucket: greengrass-component-artifacts-123456789012-region
   ```

1. Allow the core device to access component artifacts in the S3 bucket\. Each core device has a [core device IAM role](device-service-role.md) that allows it to interact with AWS IoT and send logs to the AWS Cloud\. This device role doesn't allow access to S3 buckets by default, so you must create and attach a policy that allows the core device to retrieve component artifacts from the S3 bucket\.

   If your device's role already allows access to the S3 bucket, you can skip this step\. Otherwise, create an IAM policy that allows access and attach it to the role, as follows:

   1. Create a file called `component-artifact-policy.json` and copy the following JSON into the file\. This policy allows access to all files in the S3 bucket that you created in the previous step\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the bucket to use\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
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

      Copy the policy Amazon Resource Name \(ARN\) from the policy metadata in the output\. You use this ARN to attach this policy to the core device role in the next step\.

   1. Run the following command to attach the policy to the core device role\. Replace *GreengrassV2TokenExchangeRole* with the name of the role that you specified when you ran the AWS IoT Greengrass Core software\. Replace the policy ARN with the ARN from the previous step\.

      ```
      aws iam attach-role-policy \
        --role-name GreengrassV2TokenExchangeRole \
        --policy-arn arn:aws:iam::123456789012:policy/MyGreengrassV2ComponentArtifactPolicy
      ```

      If the command has no output, it succeeded, and your core device can access artifacts that you upload to this S3 bucket\.

1. Upload the Hello World Python script artifact to the S3 bucket\. Run the following command to upload the script to the same path in the bucket where the script exists on your AWS IoT Greengrass core\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the S3 bucket\.

   ```
   aws s3 cp \
     artifacts/com.example.HelloWorld/1.0.0/hello_world.py \
     s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

   The command outputs a line that starts with `upload:` if the request succeeds\.

1. Add the artifact's Amazon S3 URI to the component recipe\. The Amazon S3 URI is composed of the bucket name and the path to the artifact object in the bucket\. Your script artifact's Amazon S3 URI is the URI that you upload the artifact to in the previous step\. This URI should look similar to the following example\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the S3 bucket\.

   ```
   s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

   To add the artifact to the recipe, add a list of `Artifacts` that contains a structure with the Amazon S3 URI\.

------
#### [ JSON ]

   ```
   "Artifacts": [
     {
       "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py"
     }
   ]
   ```

   Run the following command to open the recipe file\.

   ```
   nano recipes/com.example.HelloWorld-1.0.0.json
   ```

   Add the artifact to the recipe\. Your recipe file should look similar to the following example\.

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.HelloWorld",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "My first Greengrass component.",
     "ComponentPublisher": "Amazon",
     "ComponentConfiguration": {
       "DefaultConfiguration": {
         "Message": "world"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Run": "python3 -u {artifacts:path}/hello_world.py '{configuration:/Message}'"
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py"
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   Artifacts:
     - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

   Run the following command to open the recipe file\.

   ```
   nano recipes/com.example.HelloWorld-1.0.0.yaml
   ```

   Add the artifact to the recipe\. Your recipe file should look similar to the following example\.

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
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
           python3 -u {artifacts:path}/hello_world.py '{configuration:/Message}'
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

------

1. Create a component resource in AWS IoT Greengrass from the recipe\. Run the following command to create the component from the recipe, which you provide as a binary file\.

------
#### [ JSON ]

   ```
   aws greengrassv2 create-component-version \
     --inline-recipe fileb://recipes/com.example.HelloWorld-1.0.0.json
   ```

------
#### [ YAML ]

   ```
   aws greengrassv2 create-component-version \
     --inline-recipe fileb://recipes/com.example.HelloWorld-1.0.0.yaml
   ```

------

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

1. Verify that the component creates and is ready to be deployed\. When you create a component, its state is `REQUESTED`\. Then, AWS IoT Greengrass validates that the component is deployable\. You can run the following command to query the component status and verify that your component is deployable\. Replace the `arn` with the ARN from the previous step\.

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
     "description": "My first Greengrass component.",
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

Your Hello World component is now available in AWS IoT Greengrass\. You can deploy it back to this Greengrass core device or to other core devices\.

## Deploy your component<a name="deploy-first-component"></a>

With AWS IoT Greengrass, you can deploy components to individual devices or groups of devices\. When you deploy a component, AWS IoT Greengrass installs and runs that component's software on each target device\. You specify which components to deploy and the configuration update to deploy for each component\. You can also control how the deployment rolls out to the devices that the deployment targets\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

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

   This configuration file specifies to deploy version `1.0.0` of the Hello World component that you developed and published in the previous procedure\. The `configurationUpdate` specifies to merge the component configuration in a JSON\-encoded string\. This configuration update sets the Hello World `Message` parameter to `universe` for the device in this deployment\.

1. Run the following command to deploy the component to your Greengrass core device\. You can deploy to things, which are individual devices, or thing groups, which are groups of devices\. Replace *MyGreengrassCore* with the name of the AWS IoT thing for your core device\.

   ```
   aws greengrassv2 create-deployment \
     --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" \
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

1. Verify that the deployment completes successfully\. The deployment can take several minutes to complete\. Check the Hello World log to verify the change\. Run the following command on your Greengrass core device\.

   ```
   tail -f /tmp/Greengrass_HelloWorld.log
   ```

   You should see messages similar to the following example\.

   ```
   Hello, universe! Current time: 2020-11-30 18:18:59.153933. Greetings from your first Greengrass component.
   ```
**Note**  
If the log messages don't change, the deployment failed or didn't reach the core device\. This can occur if your core device isn't connected to the internet or doesn't have permissions to retrieve artifacts from your S3 bucket\. Run the following command on your core device to view the AWS IoT Greengrass Core software log file\. This file includes logs from the Greengrass core device's deployment service\.  

   ```
   sudo tail -f /greengrass/v2/logs/greengrass.log
   ```
For more information, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\.

You've completed this tutorial\. The AWS IoT Greengrass Core software and your Hello World component run on your device\. Also, your Hello World component is available in AWS IoT Greengrass to deploy to other devices\. For more information about the topics that this tutorial explores, see the following:
+ [Create local AWS IoT Greengrass components](create-components.md)
+ [Upload components to deploy to your core devicesUpload components to deploy](upload-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
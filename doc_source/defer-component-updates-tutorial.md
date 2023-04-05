# Tutorial: Develop a Greengrass component that defers component updates<a name="defer-component-updates-tutorial"></a>

You can complete this tutorial to develop a component that defers over\-the\-air deployment updates\. When you deploy updates to your devices, you might want to delay updates based on conditions, such as the following:
+ The device has a low battery level\.
+ The device is running a process or job that can't be interrupted\.
+ The device has a limited or expensive internet connection\.

**Note**  
A *component* is a software module that runs on AWS IoT Greengrass core devices\. Components enable you to create and manage complex applications as discrete building blocks that you can reuse from one Greengrass core device to another\.

In this tutorial, you do the following:

1. Install the Greengrass Development Kit CLI \(GDK CLI\) on your development computer\. The GDK CLI provides features that help you develop custom Greengrass components\.

1. Develop a Hello World component that defers component updates when the core device's battery level is below a threshold\. This component subscribes to update notifications using the [SubscribeToComponentUpdates](ipc-component-lifecycle.md#ipc-operation-subscribetocomponentupdates) IPC operation\. When it receives the notification, it checks if the battery level is lower than a customizable threshold\. If the battery level is below the threshold, it defers the update for 30 seconds using the [DeferComponentUpdate](ipc-component-lifecycle.md#ipc-operation-defercomponentupdate) IPC operation\. You develop this component on your development computer using the GDK CLI\.
**Note**  
This component reads battery level from a file that you create on the core device to imitate a real battery, so you can complete this tutorial on a core device without a battery\.

1. Publish that component to the AWS IoT Greengrass service\.

1. Deploy that component from the AWS Cloud to a Greengrass core device to test it\. Then, you modify the virtual battery level on the core device, and create additional deployments to see how the core device defers updates when the battery level is low\.

You can expect to spend 20–30 minutes on this tutorial\.

## Prerequisites<a name="defer-component-updates-tutorial-prerequisites"></a>

To complete this tutorial, you need the following:
+ An AWS account\. If you don't have one, see [Set up an AWS account](setting-up.md#set-up-aws-account)\.
+ An AWS Identity and Access Management \(IAM\) user with administrator permissions\.
+ A Greengrass core device with an internet connection\. For more information about how to set up a core device, see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.
  + [Python](https://www.python.org/downloads/) 3\.6 or later installed for all users on the core device and added to the `PATH` environment variable\. On Windows, you must also have the Python Launcher for Windows installed for all users\.
**Important**  <a name="windows-core-device-python-installation"></a>
In Windows, Python doesn't install for all users by default\. When you install Python, you must customize the installation to configure it for the AWS IoT Greengrass Core software to run Python scripts\. For example, if you use the graphical Python installer, do the following:  
Select **Install launcher for all users \(recommended\)**\.
Choose **Customize installation**\.
Choose **Next**\.
Select **Install for all users**\.
Select **Add Python to environment variables**\.
Choose **Install**\.
For more information, see [Using Python on Windows](https://docs.python.org/3/using/windows.html) in the *Python 3 documentation*\.
+ A Windows, macOS, or Unix\-like development computer with an internet connection\.
  + [Python](https://www.python.org/downloads/) 3\.6 or later installed on your development computer\.
  + [Git](https://git-scm.com/) installed on your development computer\.
  + <a name="development-component-aws-cli-prerequisite"></a>AWS Command Line Interface \(AWS CLI\) installed and configured with credentials on your development computer\. For more information, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS Command Line Interface User Guide*\.
**Note**  
If you use a Raspberry Pi or another 32\-bit ARM device, install AWS CLI V1\. AWS CLI V2 isn't available for 32\-bit ARM devices\. For more information, see [Installing, updating, and uninstalling the AWS CLI version 1](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv1.html)\.

## Step 1: Install the Greengrass Development Kit CLI<a name="install-gdk-cli-defer-updates"></a>

The [Greengrass Development Kit CLI \(GDK CLI\)](greengrass-development-kit-cli.md) provides features that help you develop custom Greengrass components\. You can use the GDK CLI to create, build, and publish custom components\.

If you haven't installed the GDK CLI on your development computer, complete the following steps to install it\.

**To install the latest version of the GDK CLI**

1. On your development computer, run the following command to install the latest version of the GDK CLI from its [GitHub repository](https://github.com/aws-greengrass/aws-greengrass-gdk-cli)\.

   ```
   python3 -m pip install -U git+https://github.com/aws-greengrass/aws-greengrass-gdk-cli.git@v1.2.0
   ```

1. <a name="gdk-cli-verify-installation"></a>Run the following command to verify that the GDK CLI installed successfully\.

   ```
   gdk --help
   ```

   If the `gdk` command isn't found, add its folder to PATH\.
   + On Linux devices, add `/home/MyUser/.local/bin` to PATH, and replace *MyUser* with the name of your user\.
   + On Windows devices, add `PythonPath\Scripts` to PATH, and replace *PythonPath* with the path to the Python folder on your device\.

## Step 2: Develop a component that defers updates<a name="develop-component-defer-updates"></a>

In this section, you develop a Hello World component in Python that defers component updates when the core device's battery level is below a threshold that you configure when you deploy the component\. In this component, you use the [interprocess communication \(IPC\) interface](interprocess-communication.md) in the AWS IoT Device SDK v2 for Python\. You use the [SubscribeToComponentUpdates](ipc-component-lifecycle.md#ipc-operation-subscribetocomponentupdates) IPC operation to receive notifications when the core device receives a deployment\. Then, you use the [DeferComponentUpdate](ipc-component-lifecycle.md#ipc-operation-defercomponentupdate) IPC operation to defer or acknowledge the update based on the device's battery level\.

**To develop a Hello World component that defers updates**

1. On your development computer, create a folder for the component source code\.

   ```
   mkdir com.example.BatteryAwareHelloWorld
   cd com.example.BatteryAwareHelloWorld
   ```

1. Use a text editor to create a file named `gdk-config.json`\. The GDK CLI reads from the [GDK CLI configuration file](gdk-cli-configuration-file.md), named `gdk-config.json`, to build and publish components\. This configuration file exists in the root of the component folder\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano gdk-config.json
   ```

   Copy the following JSON into the file\.
   + Replace *Amazon* with your name\.
   + Replace *us\-west\-2* with the AWS Region where your core device operates\. The GDK CLI publishes the component in this AWS Region\.
   + Replace *greengrass\-component\-artifacts* with the S3 bucket prefix to use\. When you use the GDK CLI to publish the component, the GDK CLI uploads the component's artifacts to the S3 bucket whose name is formed from this value, the AWS Region, and your AWS account ID using the following format: `bucketPrefix-region-accountId`\.

     For example, if you specify **greengrass\-component\-artifacts** and **us\-west\-2**, and your AWS account ID is **123456789012**, the GDK CLI uses the S3 bucket named `greengrass-component-artifacts-us-west-2-123456789012`\.

   ```
   {
     "component": {
       "com.example.BatteryAwareHelloWorld": {
         "author": "Amazon",
         "version": "NEXT_PATCH",
         "build": {
           "build_system" : "zip"
         },
         "publish": {
           "region": "us-west-2",
           "bucket": "greengrass-component-artifacts"
         }
       }
     },
     "gdk_version": "1.0.0"
   }
   ```

   The configuration file specifies the following:
   + The version to use when the GDK CLI publishes the Greengrass component to the AWS IoT Greengrass cloud service\. `NEXT_PATCH` specifies to choose the next patch version after the latest version available in the AWS IoT Greengrass cloud service\. If the component doesn't have a version in the AWS IoT Greengrass cloud service yet, the GDK CLI uses `1.0.0`\.
   + The build system for the component\. When you use the `zip` build system, the GDK CLI packages the component's source into a ZIP file that becomes the component's single artifact\.
   + The AWS Region where the GDK CLI publishes the Greengrass component\.
   + The prefix for the S3 bucket where the GDK CLI uploads the component's artifacts\.

1. Use a text editor to create the component source code in a file named `main.py`\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano main.py
   ```

   Copy the following Python code into the file\.

   ```
   import json
   import os
   import sys
   import time
   import traceback
   
   from pathlib import Path
   
   from awsiot.greengrasscoreipc.clientv2 import GreengrassCoreIPCClientV2
   
   HELLO_WORLD_PRINT_INTERVAL = 15  # Seconds
   DEFER_COMPONENT_UPDATE_INTERVAL = 30 * 1000  # Milliseconds
   
   
   class BatteryAwareHelloWorldPrinter():
       def __init__(self, ipc_client: GreengrassCoreIPCClientV2, battery_file_path: Path, battery_threshold: float):
           self.battery_file_path = battery_file_path
           self.battery_threshold = battery_threshold
           self.ipc_client = ipc_client
           self.subscription_operation = None
   
       def on_component_update_event(self, event):
           try:
               if event.pre_update_event is not None:
                   if self.is_battery_below_threshold():
                       self.defer_update(event.pre_update_event.deployment_id)
                       print('Deferred update for deployment %s' %
                             event.pre_update_event.deployment_id)
                   else:
                       self.acknowledge_update(
                           event.pre_update_event.deployment_id)
                       print('Acknowledged update for deployment %s' %
                             event.pre_update_event.deployment_id)
               elif event.post_update_event is not None:
                   print('Applied update for deployment')
           except:
               traceback.print_exc()
   
       def subscribe_to_component_updates(self):
           if self.subscription_operation == None:
               # SubscribeToComponentUpdates returns a tuple with the response and the operation.
               _, self.subscription_operation = self.ipc_client.subscribe_to_component_updates(
                   on_stream_event=self.on_component_update_event)
   
       def close_subscription(self):
           if self.subscription_operation is not None:
               self.subscription_operation.close()
               self.subscription_operation = None
   
       def defer_update(self, deployment_id):
           self.ipc_client.defer_component_update(
               deployment_id=deployment_id, recheck_after_ms=DEFER_COMPONENT_UPDATE_INTERVAL)
   
       def acknowledge_update(self, deployment_id):
           # Specify recheck_after_ms=0 to acknowledge a component update.
           self.ipc_client.defer_component_update(
               deployment_id=deployment_id, recheck_after_ms=0)
   
       def is_battery_below_threshold(self):
           return self.get_battery_level() < self.battery_threshold
   
       def get_battery_level(self):
           # Read the battery level from the virtual battery level file.
           with self.battery_file_path.open('r') as f:
               data = json.load(f)
               return float(data['battery_level'])
   
       def print_message(self):
           message = 'Hello, World!'
           if self.is_battery_below_threshold():
               message += ' Battery level (%d) is below threshold (%d), so the component will defer updates' % (
                   self.get_battery_level(), self.battery_threshold)
           else:
               message += ' Battery level (%d) is above threshold (%d), so the component will acknowledge updates' % (
                   self.get_battery_level(), self.battery_threshold)
           print(message)
   
   
   def main():
       # Read the battery threshold and virtual battery file path from command-line args.
       args = sys.argv[1:]
       battery_threshold = float(args[0])
       battery_file_path = Path(args[1])
       print('Reading battery level from %s and deferring updates when below %d' % (
           str(battery_file_path), battery_threshold))
   
       try:
           # Create an IPC client and a Hello World printer that defers component updates.
           ipc_client = GreengrassCoreIPCClientV2()
           hello_world_printer = BatteryAwareHelloWorldPrinter(
               ipc_client, battery_file_path, battery_threshold)
           hello_world_printer.subscribe_to_component_updates()
           try:
               # Keep the main thread alive, or the process will exit.
               while True:
                   hello_world_printer.print_message()
                   time.sleep(HELLO_WORLD_PRINT_INTERVAL)
           except InterruptedError:
               print('Subscription interrupted')
           hello_world_printer.close_subscription()
       except Exception:
           print('Exception occurred', file=sys.stderr)
           traceback.print_exc()
           exit(1)
   
   
   if __name__ == '__main__':
       main()
   ```

   This Python application does the following:
   + Reads the core device's battery level from a virtual battery level file that you'll create on the core device later\. This virtual battery level file imitates a real battery, so you can complete this tutorial on core devices that don't have a battery\.
   + Reads command\-line arguments for the battery threshold and the path to the virtual battery level file\. The component recipe sets these command\-line arguments based on configuration parameters, so you can customize these values when you deploy the component\.
   + Uses the IPC client V2 in the [AWS IoT Device SDK v2 for Python](https://github.com/aws/aws-iot-device-sdk-python-v2) to communicate with the AWS IoT Greengrass Core software\. Compared to the original IPC client, the IPC client V2 reduces the amount of code that you need to write to use IPC in custom components\.
   + Subscribes to update notifications using the [SubscribeToComponentUpdates](ipc-component-lifecycle.md#ipc-operation-subscribetocomponentupdates) IPC operation\. The AWS IoT Greengrass Core software sends notifications before and after each deployment\. The component calls the following function each time it receives a notification\. If the notification is for an upcoming deployment, the component checks if the battery level is lower than a threshold\. If the battery level is below the threshold, the component defers the update for 30 seconds using the [DeferComponentUpdate](ipc-component-lifecycle.md#ipc-operation-defercomponentupdate) IPC operation\. Otherwise, if the battery level isn't below the threshold, the component acknowledges the update, so the update can proceed\.

     ```
     def on_component_update_event(self, event):
         try:
             if event.pre_update_event is not None:
                 if self.is_battery_below_threshold():
                     self.defer_update(event.pre_update_event.deployment_id)
                     print('Deferred update for deployment %s' %
                           event.pre_update_event.deployment_id)
                 else:
                     self.acknowledge_update(
                         event.pre_update_event.deployment_id)
                     print('Acknowledged update for deployment %s' %
                           event.pre_update_event.deployment_id)
             elif event.post_update_event is not None:
                 print('Applied update for deployment')
         except:
             traceback.print_exc()
     ```
**Note**  
The AWS IoT Greengrass Core software doesn't send update notifications for local deployments, so you deploy this component using the AWS IoT Greengrass cloud service to test it\.

1. Use a text editor to create the component recipe in a file named `recipe.json` or `recipe.yaml`\. The component *recipe* defines the component's metadata, default configuration parameters, and platform\-specific lifecycle scripts\.

------
#### [ JSON ]

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano recipe.json
   ```

   Copy the following JSON into the file\.

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "COMPONENT_NAME",
     "ComponentVersion": "COMPONENT_VERSION",
     "ComponentDescription": "This Hello World component defers updates when the battery level is below a threshold.",
     "ComponentPublisher": "COMPONENT_AUTHOR",
     "ComponentConfiguration": {
       "DefaultConfiguration": {
         "BatteryThreshold": 50,
         "LinuxBatteryFilePath": "/home/ggc_user/virtual_battery.json",
         "WindowsBatteryFilePath": "C:\\Users\\ggc_user\\virtual_battery.json"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Install": "python3 -m pip install --user awsiotsdk --upgrade",
           "Run": "python3 -u {artifacts:decompressedPath}/com.example.BatteryAwareHelloWorld/main.py \"{configuration:/BatteryThreshold}\" \"{configuration:/LinuxBatteryFilePath}\""
         },
         "Artifacts": [
           {
             "Uri": "s3://BUCKET_NAME/COMPONENT_NAME/COMPONENT_VERSION/com.example.BatteryAwareHelloWorld.zip",
             "Unarchive": "ZIP"
           }
         ]
       },
       {
         "Platform": {
           "os": "windows"
         },
         "Lifecycle": {
           "Install": "py -3 -m pip install --user awsiotsdk --upgrade",
           "Run": "py -3 -u {artifacts:decompressedPath}/com.example.BatteryAwareHelloWorld/main.py \"{configuration:/BatteryThreshold}\" \"{configuration:/WindowsBatteryFilePath}\""
         },
         "Artifacts": [
           {
             "Uri": "s3://BUCKET_NAME/COMPONENT_NAME/COMPONENT_VERSION/com.example.BatteryAwareHelloWorld.zip",
             "Unarchive": "ZIP"
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano recipe.yaml
   ```

   Copy the following YAML into the file\.

   ```
   ---
   RecipeFormatVersion: "2020-01-25"
   ComponentName: "COMPONENT_NAME"
   ComponentVersion: "COMPONENT_VERSION"
   ComponentDescription: "This Hello World component defers updates when the battery level is below a threshold."
   ComponentPublisher: "COMPONENT_AUTHOR"
   ComponentConfiguration:
     DefaultConfiguration:
       BatteryThreshold: 50
       LinuxBatteryFilePath: "/home/ggc_user/virtual_battery.json"
       WindowsBatteryFilePath: "C:\\Users\\ggc_user\\virtual_battery.json"
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Install: python3 -m pip install --user awsiotsdk --upgrade
         Run: python3 -u {artifacts:decompressedPath}/com.example.BatteryAwareHelloWorld/main.py "{configuration:/BatteryThreshold}" "{configuration:/LinuxBatteryFilePath}"
       Artifacts:
         - Uri: "s3://BUCKET_NAME/COMPONENT_NAME/COMPONENT_VERSION/com.example.BatteryAwareHelloWorld.zip"
           Unarchive: ZIP
     - Platform:
         os: windows
       Lifecycle:
         Install: py -3 -m pip install --user awsiotsdk --upgrade
         Run: py -3 -u {artifacts:decompressedPath}/com.example.BatteryAwareHelloWorld/main.py "{configuration:/BatteryThreshold}" "{configuration:/WindowsBatteryFilePath}"
       Artifacts:
         - Uri: "s3://BUCKET_NAME/COMPONENT_NAME/COMPONENT_VERSION/com.example.BatteryAwareHelloWorld.zip"
           Unarchive: ZIP
   ```

------

   This recipe specifies the following:
   + Default configuration parameters for the battery threshold, the virtual battery file path on Linux core devices, and the virtual battery file path on Windows core devices\.
   + An `Install` lifecycle that installs the latest version of the AWS IoT Device SDK v2 for Python\.
   + A `Run` lifecycle that runs the Python application in `main.py`\.
   + Placeholders, such as `COMPONENT_NAME` and `COMPONENT_VERSION`, where the GDK CLI replaces information when it builds the component recipe\.

   For more information about component recipes, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.

## Step 3: Publish the component to the AWS IoT Greengrass service<a name="publish-component-defer-updates"></a>

In this section, you publish the Hello World component to the AWS IoT Greengrass cloud service\. After a component is available in the AWS IoT Greengrass cloud service, you can deploy it to core devices\. You use the GDK CLI to publish the component from your development computer to the AWS IoT Greengrass cloud service\. The GDK CLI uploads the component's recipe and artifacts for you\.

**To publish the Hello World component to the AWS IoT Greengrass service**

1. Run the following command to build the component using the GDK CLI\. The [component build command](greengrass-development-kit-cli-component.md#greengrass-development-kit-cli-component-build) creates a recipe and artifacts based on the GDK CLI configuration file\. In this process, the GDK CLI creates a ZIP file that contains the component's source code\.

   ```
   gdk component build
   ```

   You should see messages similar to the following example\.

   ```
   [2022-04-28 11:20:16] INFO - Getting project configuration from gdk-config.json
   [2022-04-28 11:20:16] INFO - Found component recipe file 'recipe.yaml' in the  project directory.
   [2022-04-28 11:20:16] INFO - Building the component 'com.example.BatteryAwareHelloWorld' with the given project configuration.
   [2022-04-28 11:20:16] INFO - Using 'zip' build system to build the component.
   [2022-04-28 11:20:16] WARNING - This component is identified as using 'zip' build system. If this is incorrect, please exit and specify custom build command in the 'gdk-config.json'.
   [2022-04-28 11:20:16] INFO - Zipping source code files of the component.
   [2022-04-28 11:20:16] INFO - Copying over the build artifacts to the greengrass component artifacts build folder.
   [2022-04-28 11:20:16] INFO - Updating artifact URIs in the recipe.
   [2022-04-28 11:20:16] INFO - Creating component recipe in 'C:\Users\finthomp\greengrassv2\com.example.BatteryAwareHelloWorld\greengrass-build\recipes'.
   ```

1. Run the following command to publish the component to the AWS IoT Greengrass cloud service\. The [component publish command](greengrass-development-kit-cli-component.md#greengrass-development-kit-cli-component-publish) uploads the component's ZIP file artifact to an S3 bucket\. Then, it updates the ZIP file's S3 URI in the component recipe and uploads the recipe to the AWS IoT Greengrass service\. In this process, the GDK CLI checks what version of the Hello World component is already available in the AWS IoT Greengrass cloud service, so it can choose the next patch version after that version\. If the component doesn't exist yet, the GDK CLI uses version `1.0.0`\.

   ```
   gdk component publish
   ```

   You should see messages similar to the following example\. The output tells you the version of the component that the GDK CLI created\.

   ```
   [2022-04-28 11:20:29] INFO - Getting project configuration from gdk-config.json
   [2022-04-28 11:20:29] INFO - Found component recipe file 'recipe.yaml' in the  project directory.
   [2022-04-28 11:20:29] INFO - Found credentials in shared credentials file: ~/.aws/credentials
   [2022-04-28 11:20:30] INFO - No private version of the component 'com.example.BatteryAwareHelloWorld' exist in the account. Using '1.0.0' as the next version to create.
   [2022-04-28 11:20:30] INFO - Publishing the component 'com.example.BatteryAwareHelloWorld' with the given project configuration.
   [2022-04-28 11:20:30] INFO - Uploading the component built artifacts to s3 bucket.
   [2022-04-28 11:20:30] INFO - Uploading component artifacts to S3 bucket: greengrass-component-artifacts-us-west-2-123456789012. If this is your first time using this bucket, add the 's3:GetObject' permission to each core device's token exchange role to allow it to download the component artifacts. For more information, see https://docs.aws.amazon.com/greengrass/v2/developerguide/device-service-role.html.
   [2022-04-28 11:20:30] INFO - Not creating an artifacts bucket as it already exists.
   [2022-04-28 11:20:30] INFO - Updating the component recipe com.example.BatteryAwareHelloWorld-1.0.0.
   [2022-04-28 11:20:31] INFO - Creating a new greengrass component com.example.BatteryAwareHelloWorld-1.0.0
   [2022-04-28 11:20:31] INFO - Created private version '1.0.0' of the component in the account.'com.example.BatteryAwareHelloWorld'.
   ```

1. Copy the S3 bucket name from the output\. You use the bucket name later to allow the core device to download component artifacts from this bucket\.

1. \(Optional\) View the component in the AWS IoT Greengrass console to verify that it uploaded successfully\. Do the following:

   1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

   1. On the **Components** page, choose the **My components** tab, and then choose **com\.example\.BatteryAwareHelloWorld**\.

      On this page, you can see the component's recipe and other information about the component\.

1. <a name="core-device-allow-s3-bucket-access-console-intro-1"></a>Allow the core device to access component artifacts in the S3 bucket\.

   <a name="core-device-allow-s3-bucket-access-console-intro-2"></a>Each core device has a [core device IAM role](device-service-role.md) that allows it to interact with AWS IoT and send logs to the AWS Cloud\. This device role doesn't allow access to S3 buckets by default, so you must create and attach a policy that allows the core device to retrieve component artifacts from the S3 bucket\.

   <a name="core-device-allow-s3-bucket-access-console-intro-3"></a>If your device's role already allows access to the S3 bucket, you can skip this step\. Otherwise, create an IAM policy that allows access and attach it to the role, as follows:

   1. <a name="core-device-allow-s3-bucket-access-console-step-1"></a>In the [IAM console](https://console.aws.amazon.com/iam) navigation menu, choose **Policies**, and then choose **Create policy**\.

   1. On the **JSON** tab, replace the placeholder content with the following policy\. Replace *greengrass\-component\-artifacts\-us\-west\-2\-123456789012* with the name of the S3 bucket where the GDK CLI uploaded the component's artifacts\.

      For example, if you specified **greengrass\-component\-artifacts** and **us\-west\-2** in the GDK CLI configuration file, and your AWS account ID is **123456789012**, the GDK CLI uses the S3 bucket named `greengrass-component-artifacts-us-west-2-123456789012`\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::greengrass-component-artifacts-us-west-2-123456789012/*"
          }
        ]
      }
      ```

   1. <a name="core-device-allow-s3-bucket-access-console-step-3"></a>Choose **Next: Tags**, and then choose **Next: Review**\.

   1. <a name="core-device-allow-s3-bucket-access-console-step-4"></a>For **Name**, enter **MyGreengrassV2ComponentArtifactPolicy**\.

   1. <a name="core-device-allow-s3-bucket-access-console-step-5"></a>Choose **Create policy**\.

   1. <a name="core-device-allow-s3-bucket-access-console-step-6"></a>In the [IAM console](https://console.aws.amazon.com/iam) navigation menu, choose **Role**, and then choose the name of the role for the core device\. You specified this role name when you installed the AWS IoT Greengrass Core software\. If you did not specify a name, the default is `GreengrassV2TokenExchangeRole`\.

   1. <a name="core-device-allow-s3-bucket-access-console-step-7"></a>Under **Permissions**, choose **Add permissions**, then choose **Attach policies**\.

   1. <a name="core-device-allow-s3-bucket-access-console-step-8"></a>On the **Add permissions** page, select the check box next to the `MyGreengrassV2ComponentArtifactPolicy` policy that you created, and then choose **Attach policies**\.

## Step 4: Deploy and test the component on a core device<a name="deploy-component-defer-updates"></a>

In this section, you deploy the component to the core device to test its functionality\. On the core device, you create the virtual battery level file to imitate a real battery\. Then, you create additional deployments and observe the component log files on the core device to see the component defer and acknowledge updates\.

**To deploy and test the Hello World component that defers updates**

1. Use a text editor to create a virtual battery level file\. This file imitates a real battery\.
   + On Linux core devices, create a file named `/home/ggc_user/virtual_battery.json`\. Run the text editor with `sudo` permissions\.
   + On Windows core devices, create a file named `C:\Users\ggc_user\virtual_battery.json`\. Run the text editor as an administrator\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   sudo nano /home/ggc_user/virtual_battery.json
   ```

   Copy the following JSON into the file\.

   ```
   {
     "battery_level": 50
   }
   ```

1. Deploy the Hello World component to the core device\. Do the following:

   1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

   1. On the **Components** page, choose the **My components** tab, and then choose **com\.example\.BatteryAwareHelloWorld**\.

   1. On the **com\.example\.BatteryAwareHelloWorld** page, choose **Deploy**\.

   1. <a name="deploy-component-choose-deployment-step"></a>From **Add to deployment**, choose an existing deployment to revise, or choose to create a new deployment, and then choose **Next**\.

   1. <a name="deploy-component-choose-target-step"></a>If you chose to create a new deployment, choose the target core device or thing group for the deployment\. On the **Specify target** page, under **Deployment target**, choose a core device or thing group, and then choose **Next**\.

   1. On the **Select components** page, verify that the **com\.example\.BatteryAwareHelloWorld** component is selected, choose **Next**\.

   1. On the **Configure components** page, select **com\.example\.BatteryAwareHelloWorld**, and then do the following:

      1. Choose **Configure component**\.

      1. In the **Configure com\.example\.BatteryAwareHelloWorld** modal, under **Configuration update**, in **Configuration to merge**, enter the following configuration update\.

         ```
         {
           "BatteryThreshold": 70
         }
         ```

      1. Choose **Confirm** to close the modal, and then choose **Next**\.

   1. On the **Confirm advanced settings** page, in the **Deployment policies** section, under **Component update policy**, confirm that **Notify components** is selected\. **Notify components** is selected by default when you create a new deployment\.

   1. <a name="deploy-component-review-and-deploy-step"></a>On the **Review** page, choose **Deploy**\.

      The deployment can take up to a minute to complete\.

1. The AWS IoT Greengrass Core software saves stdout from component processes to log files in the `logs` folder\. Run the following command to verify that the Hello World component runs and prints status messages\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.BatteryAwareHelloWorld.log
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   type C:\greengrass\v2\logs\com.example.BatteryAwareHelloWorld.log
   ```

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2\logs\com.example.BatteryAwareHelloWorld.log -Tail 10 -Wait
   ```

------

   You should see messages similar to the following example\.

   ```
   Hello, World! Battery level (50) is below threshold (70), so the component will defer updates.
   ```
**Note**  
If the file doesn't exist, the deployment may not be complete yet\. If the file doesn't exist within 30 seconds, the deployment likely failed\. This can occur if the core device doesn't have permission to download the component's artifacts from the S3 bucket, for example\. Run the following command to view the AWS IoT Greengrass Core software log file\. This file includes logs from the Greengrass core device's deployment service\.  

   ```
   sudo tail -f /greengrass/v2/logs/greengrass.log
   ```

   ```
   type C:\greengrass\v2\logs\greengrass.log
   ```
<a name="windows-cmd-type-observe-logs"></a>The `type` command writes the file's contents to the terminal\. Run this command multiple times to observe changes in the file\.

   ```
   gc C:\greengrass\v2\logs\greengrass.log -Tail 10 -Wait
   ```

1. Create a new deployment to the core device to verify that the component defers the update\. Do the following:

   1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Deployments**\.

   1. Choose the deployment that you created or revised earlier\.

   1. On the deployment page, choose **Revise**\.

   1. In the **Revise deployment** modal, choose **Revise deployment**\.

   1. Choose **Next** at each step, and then choose **Deploy**\.

1. Run the following command to view the component's logs again, and verify that it defers the update\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.BatteryAwareHelloWorld.log
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   type C:\greengrass\v2\logs\com.example.BatteryAwareHelloWorld.log
   ```

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2\logs\com.example.BatteryAwareHelloWorld.log -Tail 10 -Wait
   ```

------

   You should see messages similar to the following example\. The component defers the update for 30 seconds, so the component prints this message repeatedly\.

   ```
   Deferred update for deployment 50722a95-a05f-4e2a-9414-da80103269aa.
   ```

1. Use a text editor to edit the virtual battery level file and change the battery level to a value above the threshold, so the deployment can proceed\.
   + On Linux core devices, edit the file named `/home/ggc_user/virtual_battery.json`\. Run the text editor with `sudo` permissions\.
   + On Windows core devices, edit the file named `C:\Users\ggc_user\virtual_battery.json`\. Run the text editor as an administrator\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   sudo nano /home/ggc_user/virtual_battery.json
   ```

   Change the battery level to `80`\.

   ```
   {
     "battery_level": 80
   }
   ```

1. Run the following command to view the component's logs again, and verify that it acknowledges the update\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.BatteryAwareHelloWorld.log
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   type C:\greengrass\v2\logs\com.example.BatteryAwareHelloWorld.log
   ```

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2\logs\com.example.BatteryAwareHelloWorld.log -Tail 10 -Wait
   ```

------

   You should see messages similar to the following examples\.

   ```
   Hello, World! Battery level (80) is above threshold (70), so the component will acknowledge updates.
   Acknowledged update for deployment f9499eb2-4a40-40a7-86c1-c89887d859f1.
   ```

You've completed this tutorial\. The Hello World component defers or acknowledges updates based on the core device's battery level\. For more information about the topics that this tutorial explores, see the following:
+ [Develop AWS IoT Greengrass components](develop-greengrass-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
+ [Use the AWS IoT Device SDK to communicate with the Greengrass nucleus, other components, and AWS IoT CoreCommunicate with the Greengrass nucleus, other components, and AWS IoT Core](interprocess-communication.md)
+ [AWS IoT Greengrass Development Kit Command\-Line Interface](greengrass-development-kit-cli.md)
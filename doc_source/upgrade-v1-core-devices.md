# Upgrade Greengrass V1 core devices to Greengrass V2<a name="upgrade-v1-core-devices"></a>

After you verify that your applications and components work on an AWS IoT Greengrass V2 core device, you can install the AWS IoT Greengrass Core software v2\.x on your devices that currently run v1\.x, such as production devices\. Then, deploy Greengrass V2 components to run your Greengrass applications on the devices\.

To upgrade a fleet of devices from V1 to V2, complete these steps for each device to upgrade\. You can use thing groups to deploy V2 components to a fleet of core devices\.

**Tip**  
We recommend that you create a script to automate the upgrade process for a fleet of devices\. If you use [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) to manage your fleet, you can use Systems Manager to run that script on each device to upgrade your fleet from V1 to V2\.  
You can contact your AWS Enterprise Support representative with questions about how to best automate the upgrade process\.



## Step 1: Install the AWS IoT Greengrass Core software v2\.x<a name="install-v2-on-v1"></a>

Choose from the following options to install the AWS IoT Greengrass Core software v2\.x on a V1 core device:
+ **[Upgrade in fewer steps](#install-v2-after-uninstall)**

  To upgrade in fewer steps, you can uninstall the v1\.x software before you install the v2\.x software\.
+ **[Upgrade with minimal downtime](#install-v2-side-by-side)**

  To upgrade with minimal downtime, you can install both versions of the AWS IoT Greengrass Core software at the same time\. After you install the AWS IoT Greengrass Core software v2\.x and verify that your V2 applications operate correctly, you uninstall the AWS IoT Greengrass Core software v1\.x\. Before you choose this option, consider the additional RAM required to run both versions of the AWS IoT Greengrass Core software at the same time\.

### Uninstall AWS IoT Greengrass Core v1\.x before you install v2\.x<a name="install-v2-after-uninstall"></a>

If you want to upgrade sequentially, uninstall the AWS IoT Greengrass Core software v1\.x before you install v2\.x on your device\.

**To uninstall the AWS IoT Greengrass Core software v1\.x**

1. If the AWS IoT Greengrass Core software v1\.x is running as a service, you must stop, disable, and remove the service\.

   1. Stop the running AWS IoT Greengrass Core software v1\.x service\. 

      ```
      sudo systemctl stop greengrass 
      ```

   1. Wait until the service stops\. You can use the `list` command to check the status of the service\. 

      ```
      sudo systemctl list-units --type=service | grep greengrass
      ```

   1. Disable the service\. 

      ```
      sudo systemctl disable greengrass
      ```

   1. Remove the service\. 

      ```
      sudo rm /etc/systemd/system/greengrass.service
      ```

1. If the AWS IoT Greengrass Core software v1\.x is not running as a service, use the following command to stop the daemon\. Replace *greengrass\-root* with the name of your Greengrass root folder\. The default location is `/greengrass`\.

   ```
   cd /greengrass-root/ggc/core/
   sudo ./greengrassd stop
   ```

1. \(Optional\) Back up your Greengrass root folder and, if applicable, your [custom write folder](https://docs.aws.amazon.com/greengrass/v1/developerguide/gg-core.html#write-directory), to a different folder on your device\.

   1. Use the following command to copy the current Greengrass root folder to a different folder, and then remove the root folder\.

      ```
      sudo cp -r /greengrass-root /path/to/greengrass-backup
      rm -rf /greengrass-root
      ```

   1. Use the following command to move the write folder to a different folder, and then remove the write folder\. 

      ```
      sudo cp -r /write-directory /path/to/write-directory-backup
      rm -rf /write-directory
      ```

You can then use the [installation instructions for AWS IoT Greengrass V2](install-greengrass-core-v2.md) to install the software on your device\.

**Tip**  <a name="tip-migrate-reuse-core-device-identity"></a>
To reuse a core device's identity when you migrate it from V1 to V2, follow instructions to [install the AWS IoT Greengrass Core software with manual provisioning](manual-installation.md)\. Reuse the V1 core device's AWS IoT thing and certificate, and update the certificate's AWS IoT policies to grant permissions that the v2\.x software requires\.

### Install AWS IoT Greengrass Core software v2\.x on a device already running v1\.x<a name="install-v2-side-by-side"></a>

If you install the AWS IoT Greengrass Core v2\.x software on a device that is already running the AWS IoT Greengrass Core software v1\.x, keep the following in mind:
+ The AWS IoT thing name for your V2 core device must be unique\. Don't use the same thing name as your V1 core device\.
+ The ports that you use for the AWS IoT Greengrass Core software v2\.x must be different from the ports that you use for v1\.x\.
  + Configure the V1 stream manager to use a port other than 8088\. For more information, see [Configure stream manager](https://docs.aws.amazon.com/greengrass/v1/developerguide/configure-stream-manager.html)\.
  + Configure the V1 MQTT broker to use a port other than 8883\. For more information, see [Configure the MQTT port for local messaging](https://docs.aws.amazon.com/greengrass/v1/developerguide/gg-core.html#config-local-mqtt-port)\.
+ AWS IoT Greengrass V2 doesn't provide the option to rename the Greengrass system service\. If you run Greengrass as a system service, you must do one of the following to avoid conflicting system service names:
  + Rename the Greengrass service for v1\.x before you install v2\.x\.
  + Install the AWS IoT Greengrass Core software v2\.x without a system service, and then manually [configure the software as a system service](configure-greengrass-core-v2.md#configure-system-service) with a name other than `greengrass`\.

**To rename the Greengrass service for v1\.x**

  1. Stop the AWS IoT Greengrass Core software v1\.x service\. 

     ```
     sudo systemctl stop greengrass
     ```

  1. Wait for the service to stop\. The service can take up to a few minutes to stop\. You can use the `list-units` command to check whether the service stopped\. 

     ```
     sudo systemctl list-units --type=service | grep greengrass
     ```

  1. Disable the service\. 

     ```
     sudo systemctl disable greengrass
     ```

  1. Rename the service\. 

     ```
     sudo mv /etc/systemd/system/greengrass.service /etc/systemd/system/greengrass-v1.service
     ```

  1. Reload the service and start it\. 

     ```
     sudo systemctl daemon-reload
     sudo systemctl reset-failed
     sudo systemctl enable greengrass-v1 
     sudo systemctl start greengrass-v1
     ```

You can then use the [installation instructions for AWS IoT Greengrass V2](install-greengrass-core-v2.md) to install the software on your device\.

**Tip**  <a name="tip-migrate-reuse-core-device-identity"></a>
To reuse a core device's identity when you migrate it from V1 to V2, follow instructions to [install the AWS IoT Greengrass Core software with manual provisioning](manual-installation.md)\. Reuse the V1 core device's AWS IoT thing and certificate, and update the certificate's AWS IoT policies to grant permissions that the v2\.x software requires\.

## Step 2: Deploy AWS IoT Greengrass V2 components to the core devices<a name="deploy-v2-resources"></a>

After you install the AWS IoT Greengrass Core software v2\.x on your device, create a deployment that includes the following resources\. To deploy components to a fleet of similar devices, create a deployment for a thing group that contains those devices\.
+ Lambda function components that you created from your V1 Lambda functions\. For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\.
+ If you use V1 subscriptions, the [legacy subscription router component](legacy-subscription-router-component.md)\.
+ If you use stream manager, the [stream manager component](stream-manager-component.md)\. For more information, see [Manage data streams on Greengrass core devices](manage-data-streams.md)\.
+ If you use local secrets, the [secret manager component](secret-manager-component.md)\.
+ If you use V1 connectors, the [AWS\-provided connector components](set-up-v2-test-device.md#use-v1-connectors)\.
+ If you use Docker containers, the [Docker application manager component](docker-application-manager-component.md)\. For more information, see [Run a Docker container](run-docker-container.md)\.
+ If you use machine learning inference, components for machine learning support\. For more information, see [Perform machine learning inference](perform-machine-learning-inference.md)\.
+ If you use connected devices, the [components for client device support](client-device-components.md)\. You must also enable client device support and associate the client devices with your core device\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.
+ If you use device shadows, the [shadow manager component](shadow-manager-component.md)\. For more information, see [Interact with device shadows](interact-with-shadows.md)\.
+ If you upload logs from Greengrass core devices to Amazon CloudWatch Logs, the [log manager component](log-manager-component.md)\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.
+ If you integrate with AWS IoT SiteWise, [follow instructions](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/configure-gateway-ggv2.html) to set up the V2 core device as an AWS IoT SiteWise gateway\. AWS IoT SiteWise provides an installation script that deploys the AWS IoT SiteWise components for you\.
+ User\-defined components that you developed to implement custom functionality\.

For information about creating and revising deployments, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\. 
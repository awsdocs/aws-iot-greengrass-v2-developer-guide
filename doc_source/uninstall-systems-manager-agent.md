# Uninstall the AWS Systems Manager Agent<a name="uninstall-systems-manager-agent"></a>

If you no longer want to manage a Greengrass core device with AWS Systems Manager, you can deregister the core device from Systems Manager and uninstall the AWS Systems Manager Agent \(Systems Manager Agent\) from the device\.

You can reregister a core device again at any time\. To do so, deploy the Systems Manager Agent component again, which registers the core device with Systems Manager when it installs\. Systems Manager stores the command history for a deregistered core device for 30 days\.

**Topics**
+ [Step 1: Deregister the core device from Systems Manager](#deregister-core-device-ssm)
+ [Step 2: Uninstall the Systems Manager Agent component](#uninstall-ssm-agent-component)
+ [Step 3: Uninstall the Systems Manager Agent software](#uninstall-ssm-agent-software)

## Step 1: Deregister the core device from Systems Manager<a name="deregister-core-device-ssm"></a>

You can use the Systems Manager console or API to deregister the core device\. For more information, see [Deregistering managed nodes](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-managed-instances-advanced-deregister.html) in the *AWS Systems Manager User Guide*\.

## Step 2: Uninstall the Systems Manager Agent component<a name="uninstall-ssm-agent-component"></a>

After you deregister the core device, uninstall the [Systems Manager Agent component](systems-manager-agent-component.md) from the device\. To remove a component from a Greengrass core device, revise the deployment that installed the component, and remove the component from the deployment\. The AWS IoT Greengrass Core software uninstalls a component when none of a core device's deployments specify that component\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

### To uninstall the Systems Manager Agent component \(console\)<a name="uninstall-ssm-agent-component-console"></a>

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Core devices**\.

1. Choose the core device where you want to uninstall the Systems Manager Agent component\.

1. On the core device details page, choose the **Deployments** tab\.

1. Choose the deployment that deploys the Systems Manager Agent component to the core device\.

1. On the deployment details page, choose **Revise**\.

1. In the **Revise deployment** modal, choose **Revise deployment**\.

1. In **Step 1: Specify target**, choose **Next**\.

1. In **Step 2: Select components**, clear the selection for the **aws\.greengrass\.SystemsManagerAgent** component, and then choose **Next**\.

1. In **Step 3: Configure components**, choose **Next**\.

1. In **Step 4: Configure advanced settings**, choose **Next**\.

1. In **Step 5: Review**, choose **Deploy**\.

### To uninstall the Systems Manager Agent component \(CLI\)<a name="uninstall-ssm-agent-component-cli"></a>

To uninstall the Systems Manager Agent component, revise the deployment that deploys it, and remove it from the deployment\. For more information, see [Revise deployments](revise-deployments.md)\.

The deployment can take several minutes to complete\. You can use the AWS IoT Greengrass service to check the status of the deployment\. For more information, see [Check deployment status](check-deployment-status.md)\.

## Step 3: Uninstall the Systems Manager Agent software<a name="uninstall-ssm-agent-software"></a>

The Systems Manager Agent software continues to run on the core device after you remove the Systems Manager Agent component\. To remove the Systems Manager Agent software, you can run commands on the core device\. For more information, see [Uninstall Systems Manager Agent from Linux instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-uninstall-agent.html) in the *AWS Systems Manager User Guide*\.
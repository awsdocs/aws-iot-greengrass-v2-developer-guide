# Manage Greengrass core devices with AWS Systems Manager<a name="manage-with-systems-manager"></a>

**Note**  
AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 

Systems Manager is an AWS service that you can use to view and control your infrastructure on AWS, including Amazon EC2 instances, on\-premises servers and virtual machines \(VMs\), and edge devices\. Systems Manager enables you to view operational data, automate operation tasks, and maintain security and compliance\. When you register a machine with Systems Manager, it's called a *managed node*\. For more information, see [What is AWS Systems Manager?](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) in the *AWS Systems Manager User Guide*\.

The AWS Systems Manager Agent \(Systems Manager Agent\) is software that you can install on devices to enable Systems Manager to update, manage, and configure them\. To install the Systems Manager Agent on Greengrass core devices, deploy the [Systems Manager Agent component](systems-manager-agent-component.md)\. When you deploy the Systems Manager Agent for the first time, it registers the core device as a Systems Manager managed node\. The Systems Manager Agent runs on the device to enable communication with the Systems Manager service in the AWS Cloud\. For more information about how to install and configure the Systems Manager Agent component, see [Install the AWS Systems Manager Agent](install-systems-manager-agent.md)\.

<a name="about-systems-manager-capabilities"></a>Systems Manager tools and features are called *capabilities*\. Greengrass core devices support all Systems Manager capabilities\. For more information about these capabilities and how to use Systems Manager to manage core devices, see [Systems Manager capabilities](https://docs.aws.amazon.com/systems-manager/latest/userguide/features.html) in the *AWS Systems Manager User Guide*\.

AWS Systems Manager offers a standard\-instances tier and an advanced\-instances tier for Systems Manager managed nodes\. If you're using Systems Manager for the first time, you start on the standard\-instances tier\. On the standard\-instances tier, you can register up to 1,000 managed nodes per AWS Region in your AWS account\. If you need to register more than 1,000 managed nodes in a single account and Region, or if you need to use the [Session Manager capability](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html), use the advanced\-instances tier\. For more information, see [Configuring instance tiers](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-managed-instances-tiers.html) in the *AWS Systems Manager User Guide*\.

**Topics**
+ [Install the AWS Systems Manager Agent](install-systems-manager-agent.md)
+ [Uninstall the AWS Systems Manager Agent](uninstall-systems-manager-agent.md)
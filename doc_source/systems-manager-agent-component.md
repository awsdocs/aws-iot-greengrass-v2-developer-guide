# Systems Manager Agent<a name="systems-manager-agent-component"></a>

The AWS Systems Manager Agent component \(`aws.greengrass.SystemsManagerAgent`\) installs the Systems Manager Agent, so you can manage core devices with Systems Manager\. Systems Manager is an AWS service that you can use to view and control your infrastructure on AWS, including Amazon EC2 instances, on\-premises servers and virtual machines \(VMs\), and edge devices\. Systems Manager enables you to view operational data, automate operation tasks, and maintain security and compliance\. For more information, see [What is AWS Systems Manager?](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) and [About Systems Manager Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/prereqs-ssm-agent.html) in the *AWS Systems Manager User Guide*\.

<a name="about-systems-manager-capabilities"></a>Systems Manager tools and features are called *capabilities*\. Greengrass core devices support all Systems Manager capabilities\. For more information about these capabilities and how to use Systems Manager to manage core devices, see [Systems Manager capabilities](https://docs.aws.amazon.com/systems-manager/latest/userguide/features.html) in the *AWS Systems Manager User Guide*\.

**Topics**
+ [Versions](#systems-manager-agent-component-versions)
+ [Type](#systems-manager-agent-component-type)
+ [Operating system](#systems-manager-agent-component-os-support)
+ [Requirements](#systems-manager-agent-component-requirements)
+ [Dependencies](#systems-manager-agent-component-dependencies)
+ [Configuration](#systems-manager-agent-component-configuration)
+ [Local log file](#systems-manager-agent-component-log-file)
+ [See also](#systems-manager-agent-component-see-also)
+ [Changelog](#systems-manager-agent-component-changelog)

## Versions<a name="systems-manager-agent-component-versions"></a>

This component has the following versions:
+ 1\.0\.x

## Type<a name="systems-manager-agent-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="systems-manager-agent-component-os-support"></a>

This component can be installed on Linux core devices only\.

## Requirements<a name="systems-manager-agent-component-requirements"></a>

This component has the following requirements:
+ A Greengrass core device that runs on a 64\-bit Linux platform: Armv8 \(AArch64\) or x86\_64\.
+ You must have an AWS Identity and Access Management \(IAM\) service role that Systems Manager can assume\. This role must include the [AmazonSSMManagedInstanceCore](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore) managed policy or a custom policy that defines equivalent permissions\. For more information, see [Create an IAM service role for edge devices](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up-edge-devices-service-role.html) in the *AWS Systems Manager User Guide*\.

  When you deploy this component, you must specify this role's name for the `SSMRegistrationRole` configuration parameter\.
+ The [Greengrass device role](device-service-role.md) must allow the `ssm:AddTagsToResource` and `ssm:RegisterManagedInstance` actions\. The device role must also allow the `iam:PassRole` action for the IAM service role that fulfills the previous requirement\. The following example IAM policy grants these permissions\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "iam:PassRole"
        ],
        "Effect": "Allow",
        "Resource": [
          "arn:aws:iam::account-id:role/SSMServiceRole"
        ]
      },
      {
        "Action": [
          "ssm:AddTagsToResource",
          "ssm:RegisterManagedInstance"
        ],
        "Effect": "Allow",
        "Resource": "*"
      }
    ]
  }
  ```

### Endpoints and ports<a name="systems-manager-agent-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `ec2messages.region.amazonaws.com`  | 443 | Yes |  Communicate with the Systems Manager service in the AWS Cloud\.  | 
|  `ssm.region.amazonaws.com`  | 443 | Yes |  Register the core device as a Systems Manager managed node\.  | 
|  `ssmmessages.region.amazonaws.com`  | 443 | Yes |  Communicate with Session Manager, a capability of Systems Manager, in the AWS Cloud\.  | 

For more information, see [Reference: ec2messages, ssmmessages, and other API calls](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up-messageAPIs.html) in the *AWS Systems Manager User Guide*\.

## Dependencies<a name="systems-manager-agent-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#systems-manager-agent-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for version 1\.0\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0 | Soft | 

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="systems-manager-agent-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`SSMRegistrationRole`  
The IAM service role that Systems Manager can assume and that includes the [AmazonSSMManagedInstanceCore](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore) managed policy or a custom policy that defines equivalent permissions\. For more information, see [Create an IAM service role for edge devices](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up-edge-devices-service-role.html) in the *AWS Systems Manager User Guide*\.

`SSMOverrideExistingRegistration`  
\(Optional\) If the core device already runs the Systems Manager Agent registered with a hybrid activation, you can override the device's existing Systems Manager Agent registration\. Set this option to `true` to register the core device as a managed node using the Systems Manager Agent that this component provides\.  
This option applies only to devices that are registered with a hybrid activation\. If the core device runs on an Amazon EC2 instance with the Systems Manager Agent installed and an instance profile role configured, the Amazon EC2 instance's existing managed node ID starts with `i-`\. When you install the Systems Manager Agent component, the Systems Manager agent registers a new managed node whose ID starts with `mi-` instead of `i-`\. Then, you can use the managed node whose ID starts with `mi-` to manage the core device with Systems Manager\.
Default: `false`

`SSMResourceTags`  
\(Optional\) The tags to add to the Systems Manager managed node that this component creates for the core device\. You can use these tags to manage groups of core devices with Systems Manager\. For example, you can run a command on all devices that have a tag that you specify\.  
Specify a list where each tag is an object with a `Key` and a `Value`\. For example, the following value for `SSMResourceTags` instructs this component to set the **Owner** tag to **richard\-roe** on the core device's managed node\.  

```
[
  {
    "Key": "Owner",
    "Value": "richard-roe"
  }
]
```
This component ignores these tags if the managed node already exists and `SSMOverrideExistingRegistration` is `false`\.

**Example: Configuration merge update**  
The following example configuration specifies to use a service role named `SSMServiceRole` to allow the core device to register and communicate with Systems Manager\.  

```
{
  "SSMRegistrationRole": "SSMServiceRole",
  "SSMOverrideExistingRegistration": false,
  "SSMResourceTags": [
    {
      "Key": "Owner",
      "Value": "richard-roe"
    },
    {
      "Key": "Team",
      "Value": "solar"
    }
  ]
}
```

## Local log file<a name="systems-manager-agent-component-log-file"></a>

The Systems Manager Agent software writes logs to a folder outside the Greengrass root folder\. For more information, see [Viewing Systems Manager Agent logs](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-agent-logs.html) in the *AWS Systems Manager User Guide*\.

The Systems Manager Agent component uses shell scripts to install, start, and stop the Systems Manager Agent\. You can find the output from these scripts in the following log file\.

```
/greengrass/v2/logs/aws.greengrass.SystemsManagerAgent.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.SystemsManagerAgent.log
  ```

## See also<a name="systems-manager-agent-component-see-also"></a>
+ [Manage Greengrass core devices with AWS Systems Manager](manage-with-systems-manager.md)
+ [What is AWS Systems Manager?](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) in the *AWS Systems Manager User Guide*
+ [About Systems Manager Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/prereqs-ssm-agent.html) in the *AWS Systems Manager User Guide*

## Changelog<a name="systems-manager-agent-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  1\.0\.0  |  Initial version\.  | 
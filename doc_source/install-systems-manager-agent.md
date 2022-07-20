# Install the AWS Systems Manager Agent<a name="install-systems-manager-agent"></a>

The AWS Systems Manager Agent \(Systems Manager Agent\) is Amazon software that you install to enable Systems Manager to update, manage, and configure Greengrass core devices, Amazon EC2 instances, and other resources\. The agent processes and runs requests from the Systems Manager service in the AWS Cloud\. Then, the agent sends status and runtime information back to the Systems Manager service\. For more information, see [About Systems Manager Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/prereqs-ssm-agent.html) in the *AWS Systems Manager User Guide*\.

AWS provides the Systems Manager Agent as a Greengrass component that you can deploy to your Greengrass core devices to manage them with Systems Manager\. The [Systems Manager Agent component](systems-manager-agent-component.md) installs the Systems Manager Agent software and registers the core device as a managed node in Systems Manager\. Follow the steps on this page to complete prerequisites and deploy the Systems Manager Agent component to a core device or group of core devices\.

**Topics**
+ [Step 1: Complete general Systems Manager setup steps](#setup-systems-manager)
+ [Step 2: Create an IAM service role for Systems Manager](#create-ssm-service-role)
+ [Step 3: Add permissions to the token exchange role](#update-token-exchange-role-ssm-agent)
+ [Step 4: Deploy the Systems Manager Agent component](#deploy-ssm-agent-component)
+ [Step 5: Verify core device registration with Systems Manager](#verify-ssm-registration)

## Step 1: Complete general Systems Manager setup steps<a name="setup-systems-manager"></a>

If you haven't already done so, complete general setup steps for AWS Systems Manager\. For more information, see [Complete general Systems Manager setup steps](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-edge-devices-setup-general.html) in the *AWS Systems Manager User Guide*\.

## Step 2: Create an IAM service role for Systems Manager<a name="create-ssm-service-role"></a>

The Systems Manager Agent uses an AWS Identity and Access Management \(IAM\) service role to communicate with AWS Systems Manager\. Systems Manager assumes this role to enable Systems Manager capabilities on each core device\. The Systems Manager Agent component also uses this role to register the core device as a Systems Manager managed node when you deploy the component\. If you haven't already done so, create a Systems Manager service role for the Systems Manager Agent component to use\. For more information, see [Create an IAM service role for edge devices](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up-edge-devices-service-role.html) in the *AWS Systems Manager User Guide*\.

## Step 3: Add permissions to the token exchange role<a name="update-token-exchange-role-ssm-agent"></a>

Greengrass core devices use an IAM service role, called the token exchange role, to interact with AWS services\. Each core device has a token exchange role that you create when you [install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\. Many Greengrass components, such as the Systems Manager Agent, require additional permissions on this role\. The Systems Manager agent component requires the following permissions, which include permission to use the role that you created in [Step 2: Create an IAM service role for Systems Manager](#create-ssm-service-role)\.

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

If you haven't already done so, add these permissions to the core device's token exchange role to allow the Systems Manager Agent to operate\. You can add a new policy to the token exchange role to grant this permission\.

### To add permissions to the token exchange role \(console\)<a name="update-token-exchange-role-ssm-agent-console"></a>

1. In the [IAM console](https://console.aws.amazon.com/iam) navigation menu, choose **Roles**\.

1. Choose the IAM role that you set up as a token exchange role when you installed the AWS IoT Greengrass Core software\. If you didn't specify a name for the token exchange role when you installed the AWS IoT Greengrass Core software, it created a role named `GreengrassV2TokenExchangeRole`\.

1. Under **Permissions**, choose **Add permissions**, and then choose **Attach policies**\.

1. Choose **Create policy**\. The **Create policy** page opens in a new browser tab\.

1. On the **Create policy** page, do the following:

   1. Choose **JSON** to open the JSON editor\.

   1. Paste the following policy into the JSON editor\. Replace *SSMServiceRole* with the name of the service role that you created in [Step 2: Create an IAM service role for Systems Manager](#create-ssm-service-role)\.

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

   1. Choose **Next: Tags**\.

   1. Choose **Next: Review**\.

   1. Enter a **Name** for the policy, such as **GreengrassSSMAgentComponentPolicy**\.

   1. Choose **Create policy**\.

   1. Switch to the previous browser tab where you have the token exchange role open\.

1. On the **Add permissions** page, choose the refresh button, and then select the Greengrass Systems Manager agent policy that you created in the previous step\.

1. Choose **Attach policies**\.

   The core devices that use this token exchange role now have permission to interact with the Systems Manager service\.

### To add permissions to the token exchange role \(AWS CLI\)<a name="update-token-exchange-role-ssm-agent-cli"></a>

**To add a policy that grants permission to use Systems Manager**

1. Create a file called `ssm-agent-component-policy.json` and copy the following JSON into the file\. Replace *SSMServiceRole* with the name of the service role that you created in [Step 2: Create an IAM service role for Systems Manager](#create-ssm-service-role)\.

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

1. Run the following command to create the policy from the policy document in `ssm-agent-component-policy.json`\.

------
#### [ Linux or Unix ]

   ```
   aws iam create-policy \
     --policy-name GreengrassSSMAgentComponentPolicy \
     --policy-document file://ssm-agent-component-policy.json
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws iam create-policy ^
     --policy-name GreengrassSSMAgentComponentPolicy ^
     --policy-document file://ssm-agent-component-policy.json
   ```

------
#### [ PowerShell ]

   ```
   aws iam create-policy `
     --policy-name GreengrassSSMAgentComponentPolicy `
     --policy-document file://ssm-agent-component-policy.json
   ```

------

   Copy the policy Amazon Resource Name \(ARN\) from the policy metadata in the output\. You use this ARN to attach this policy to the core device role in the next step\.

1. Run the following command to attach the policy to the token exchange role\.
   + Replace *GreengrassV2TokenExchangeRole* with the name of the token exchange role that you specified when you installed the AWS IoT Greengrass Core software\. If you didn't specify a name for the token exchange role when you installed the AWS IoT Greengrass Core software, it created a role named `GreengrassV2TokenExchangeRole`\.
   + Replace the policy ARN with the ARN from the previous step\.

------
#### [ Linux or Unix ]

   ```
   aws iam attach-role-policy \
     --role-name GreengrassV2TokenExchangeRole \
     --policy-arn arn:aws:iam::123456789012:policy/GreengrassSSMAgentComponentPolicy
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws iam attach-role-policy ^
     --role-name GreengrassV2TokenExchangeRole ^
     --policy-arn arn:aws:iam::123456789012:policy/GreengrassSSMAgentComponentPolicy
   ```

------
#### [ PowerShell ]

   ```
   aws iam attach-role-policy `
     --role-name GreengrassV2TokenExchangeRole `
     --policy-arn arn:aws:iam::123456789012:policy/GreengrassSSMAgentComponentPolicy
   ```

------

   If the command has no output, it succeeded\. The core devices that use this token exchange role now have permission to interact with the Systems Manager service\.

## Step 4: Deploy the Systems Manager Agent component<a name="deploy-ssm-agent-component"></a>

Complete the following steps to deploy and configure the Systems Manager Agent component\. You can deploy the component to a single core device or to a group of core devices\.

### To deploy the Systems Manager Agent component \(console\)<a name="deploy-ssm-agent-component-console"></a>

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

1. On the **Components** page, choose the **Public components** tab, and then choose **aws\.greengrass\.SystemsManagerAgent**\.

1. On the **aws\.greengrass\.SystemsManagerAgent** page, choose **Deploy**\.

1. <a name="deploy-component-choose-deployment-step"></a>From **Add to deployment**, choose an existing deployment to revise, or choose to create a new deployment, and then choose **Next**\.

1. <a name="deploy-component-choose-target-step"></a>If you chose to create a new deployment, choose the target core device or thing group for the deployment\. On the **Specify target** page, under **Deployment target**, choose a core device or thing group, and then choose **Next**\.

1. On the **Select components** page, verify that the **aws\.greengrass\.SystemsManagerAgent** component is selected, choose **Next**\.

1. On the **Configure components** page, select **aws\.greengrass\.SystemsManagerAgent**, and then do the following:

   1. Choose **Configure component**\.

   1. In the **Configure aws\.greengrass\.SystemsManagerAgent** modal, under **Configuration update**, in **Configuration to merge**, enter the following configuration update\. Replace *SSMServiceRole* with the name of the service role that you created in [Step 2: Create an IAM service role for Systems Manager](#create-ssm-service-role)\.

      ```
      {
        "SSMRegistrationRole": "SSMServiceRole",
        "SSMOverrideExistingRegistration": false
      }
      ```
**Note**  <a name="deploy-ssm-agent-component-configuration-options"></a>
If the core device already runs the Systems Manager Agent registered with a hybrid activation, change `SSMOverrideExistingRegistration` to `true`\. This parameter specifies whether the Systems Manager Agent component registers the core device when the Systems Manager Agent is already running on the device with a hybrid activation\.  
You can also specify tags \(`SSMResourceTags`\) to add to the Systems Manager managed node that the Systems Manager Agent component creates for the core device\. For more information, see [Systems Manager Agent component configuration](systems-manager-agent-component.md#systems-manager-agent-component-configuration)\.

   1. Choose **Confirm** to close the modal, and then choose **Next**\.

1. <a name="deploy-component-configure-advanced-settings-step"></a>On the **Configure advanced settings** page, keep the default configuration settings, and choose **Next**\.

1. <a name="deploy-component-review-and-deploy-step"></a>On the **Review** page, choose **Deploy**\.

   The deployment can take up to a minute to complete\.

### To deploy the Systems Manager Agent component \(AWS CLI\)<a name="deploy-ssm-agent-component-cli"></a>

To deploy the Systems Manager Agent component, create a deployment document that includes `aws.greengrass.SystemsManagerAgent` in the `components` object, and specify the configuration update for the component\. Follow instructions in [Create deployments](create-deployments.md) to create a new deployment or revise an existing deployment\.

The following example partial deployment document specifies to use a service role named `SSMServiceRole`\. Replace *SSMServiceRole* with the name of the service role that you created in [Step 2: Create an IAM service role for Systems Manager](#create-ssm-service-role)\.

```
{
  ...,
  "components": {
    ...,
    "aws.greengrass.SystemsManagerAgent": {
      "componentVersion": "1.0.0",
      "configurationUpdate": {
        "merge": "{\"SSMRegistrationRole\":\"SSMServiceRole\",\"SSMOverrideExistingRegistration\":false}"
      }
    }
  }
}
```

**Note**  <a name="deploy-ssm-agent-component-configuration-options"></a>
If the core device already runs the Systems Manager Agent registered with a hybrid activation, change `SSMOverrideExistingRegistration` to `true`\. This parameter specifies whether the Systems Manager Agent component registers the core device when the Systems Manager Agent is already running on the device with a hybrid activation\.  
You can also specify tags \(`SSMResourceTags`\) to add to the Systems Manager managed node that the Systems Manager Agent component creates for the core device\. For more information, see [Systems Manager Agent component configuration](systems-manager-agent-component.md#systems-manager-agent-component-configuration)\.

The deployment can take several minutes to complete\. You can use the AWS IoT Greengrass service to check the status of the deployment, and you can check the AWS IoT Greengrass Core software logs and Systems Manager Agent component logs to verify that the Systems Manager Agent runs successfully\. For more information, see the following:
+ [Check deployment status](check-deployment-status.md)
+ [Monitor AWS IoT Greengrass logs](monitor-logs.md)
+ [Viewing Systems Manager Agent logs](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-agent-logs.html) in the *AWS Systems Manager User Guide*

If the deployment fails or the Systems Manager Agent doesn't run, you can troubleshoot the deployment on each core device\. For more information, see the following:
+ [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)
+ [Troubleshooting Systems Manager Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-ssm-agent.html) in the *AWS Systems Manager User Guide*

## Step 5: Verify core device registration with Systems Manager<a name="verify-ssm-registration"></a>

When the Systems Manager Agent component runs, it registers the core device as a managed node in Systems Manager\. You can use the AWS IoT Greengrass console, Systems Manager console, and Systems Manager API to verify that a core device is registered as a managed node\. Managed nodes are also called instances in parts of the AWS console and API\.

### To verify core device registration \(AWS IoT Greengrass console\)<a name="verify-ssm-registration-greengrass-console"></a>

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Core devices**\.

1. Choose the core device to verify\.

1. On the core device's details page, find the **AWS Systems Manager instance** property\. If this property is present and displays a link to the Systems Manager console, the core device is registered as a managed node\.

   You can also find the **AWS Systems Manager ping status** property to check the status of the Systems Manager Agent on the core device\. When the status is **Online**, you can manage the core device with Systems Manager\.

### To verify core device registration \(Systems Manager console\)<a name="verify-ssm-registration-ssm-console"></a>

1. In the [Systems Manager console](https://console.aws.amazon.com/systems-manager) navigation menu, choose **Fleet Manager**\.

1. Under **Managed nodes**, do the following:

   1. Add a filter where **Source type** is **AWS::IoT::Thing**\.

   1. Add a filter where **Source ID** is the name of the core device to verify\.

1. Find the core device in the **Managed nodes** table\. If the core device is in the table, it's registered as a managed node\.

   You can also find the **Systems Manager Agent ping status** property to check the status of the Systems Manager Agent on the core device\. When the status is **Online**, you can manage the core device with Systems Manager\.

### To verify core device registration \(AWS CLI\)<a name="verify-ssm-registration-cli"></a>
+ Use the [DescribeInstanceInformation](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_DescribeInstanceInformation.html) operation to get the list of managed nodes that match a filter that you specify\. Run the following command to verify whether a core device is registered as a managed node\. Replace *MyGreengrassCore* with the name of the core device to verify\.

  ```
  aws ssm describe-instance-information --filter Key=SourceIds,Values=MyGreengrassCore Key=SourceTypes,Values=AWS::IoT::Thing
  ```

  The response contains the list of managed nodes that match the filter\. If the list contains a managed node, the core device is registered as a managed node\. You can also find other information about the core device's managed node in the response\. If the `PingStatus` property is `Online`, you can manage the core device with Systems Manager\.

After you verify that a core device is registered as a managed node in Systems Manager, you can use the Systems Manager console and API to manage that core device\. For more information about the Systems Manager capabilities that you can use to manage Greengrass core devices, see [Systems Manager capabilities](https://docs.aws.amazon.com/systems-manager/latest/userguide/features.html) in the *AWS Systems Manager User Guide*\.
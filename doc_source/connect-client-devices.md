# Connect client devices to core devices<a name="connect-client-devices"></a>

You can configure *cloud discovery* to connect client devices to core devices\. When you configure cloud discovery, client devices can connect to the AWS IoT Greengrass cloud service to retrieve information about core devices to which they can connect\. Then, the client devices can attempt to connect to each core device until they successfully connect\.

To use cloud discovery, you must do the following:
+ Associate client devices to the core devices to which they can connect\.
+ Specify the MQTT broker endpoints where client devices can connect to each core device\.
+ Deploy components to the core device that enable support for client devices\.

  You can also deploy optional components to do the following:
  + Relay messages between client devices, Greengrass components, and the AWS IoT Core cloud service\.
  + Automatically manage core device MQTT broker endpoints for you\.
  + Manage local client device shadows and synchronize shadows with the AWS IoT Core cloud service\.

You must also review and update the core device's AWS IoT policy to verify that it has the permissions required to connect client devices\. For more information, see [Requirements](#connect-client-devices-requirements)\.

After you configure cloud discovery, you can test communications between a client device and a core device\. For more information, see [Test client device communications](test-client-device-communications.md)\.

**Topics**
+ [Requirements](#connect-client-devices-requirements)
+ [Greengrass components for client device support](#cloud-discovery-components)
+ [Configure cloud discovery \(console\)](#configure-cloud-discovery-console)
+ [Configure cloud discovery \(AWS CLI\)](#configure-cloud-discovery-cli)
+ [Associate client devices](associate-client-devices.md)
+ [Manage core device endpoints](manage-core-device-endpoints.md)
+ [Choose an MQTT broker](choose-local-mqtt-broker.md)
+ [Test client device communications](test-client-device-communications.md)
+ [Greengrass discovery RESTful API](greengrass-discover-api.md)

## Requirements<a name="connect-client-devices-requirements"></a>

To connect client devices to a core device, you must have the following:
+ The core device must run [Greengrass nucleus](greengrass-nucleus-component.md) v2\.2\.0 or later\.
+ The Greengrass service role associated with AWS IoT Greengrass for your AWS account in the AWS Region where the core device operates\. For more information, see [Configure the Greengrass service role](#configure-service-role-requirement)\.
+ The core device's AWS IoT policy must allow the following permissions:<a name="core-device-iot-policy-client-device-permissions"></a>
  + <a name="core-device-iot-policy-client-device-permissions-putcertificateauthorities"></a>`greengrass:PutCertificateAuthorities`
  + <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceidentity"></a>`greengrass:VerifyClientDeviceIdentity`
  + <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceiotcertificateassociation"></a>`greengrass:VerifyClientDeviceIoTCertificateAssociation`
  + <a name="core-device-iot-policy-client-device-permissions-getconnectivityinfo"></a>`greengrass:GetConnectivityInfo`
  + <a name="core-device-iot-policy-client-device-permissions-updateconnectivityinfo"></a>`greengrass:UpdateConnectivityInfo` – \(Optional\) This permission is required to use the [IP detector component](ip-detector-component.md), which reports the core device's network connectivity information to the AWS IoT Greengrass cloud service\.
  + <a name="core-device-iot-policy-client-device-permissions-shadows"></a>`iot:GetThingShadow`, `iot:UpdateThingShadow`, and `iot:DeleteThingShadow` – \(Optional\) These permissions are required to use the [shadow manager component](shadow-manager-component.md) to sync client device shadows with AWS IoT Core\. This feature requires [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later, shadow manager v2\.2\.0 or later, and [MQTT bridge](mqtt-bridge-component.md) v2\.2\.0 or later\.

  For more information, see [Configure the AWS IoT thing policy](#configure-iot-policy-requirement)\.
**Note**  
If you used the default AWS IoT policy when you [installed the AWS IoT Greengrass Core software](install-greengrass-core-v2.md), the core device has an AWS IoT policy that allows access to all AWS IoT Greengrass actions \(`greengrass:*`\)\.
+ AWS IoT things that you can connect as client devices\. For more information, see [Create AWS IoT resources](https://docs.aws.amazon.com/iot/latest/developerguide/create-iot-resources.html) in the *AWS IoT Core Developer Guide*\.
+ Each client device's AWS IoT policy must allow the `greengrass:Discover` permission\. For more information, see [Minimal AWS IoT policy for client devices](device-auth.md#client-device-minimal-iot-policy)\.

**Topics**
+ [Configure the Greengrass service role](#configure-service-role-requirement)
+ [Configure the AWS IoT thing policy](#configure-iot-policy-requirement)

### Configure the Greengrass service role<a name="configure-service-role-requirement"></a>

<a name="greengrass-service-role-intro"></a>The Greengrass service role is an AWS Identity and Access Management \(IAM\) service role that authorizes AWS IoT Greengrass to access resources from AWS services on your behalf\. This role makes it possible for AWS IoT Greengrass to verify the identity of client devices and manage core device connectivity information\.

If you haven't previously set up the [Greengrass service role](greengrass-service-role.md) in this Region, you must associate a Greengrass service role with AWS IoT Greengrass for your AWS account in this Region\.

When you use the **Configure core device discovery** page in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass), AWS IoT Greengrass sets up the Greengrass service role for you\. Otherwise, you can manually set it up using the [AWS IoT console](https://console.aws.amazon.com/iot) or AWS IoT Greengrass API\.

In this section, you check whether the Greengrass service role is set up\. If it isn't set up, you create a new Greengrass service role to associate with AWS IoT Greengrass for your AWS account in this Region\.

#### Configure the Greengrass service role \(console\)<a name="configure-service-role-requirement-console"></a>

1. Check if the Greengrass service role is associated with AWS IoT Greengrass for your AWS account in this Region\. Do the following:

   1. <a name="open-iot-console"></a>Navigate to the [AWS IoT console](https://console.aws.amazon.com/iot)\.

   1. In the navigation pane, choose **Settings**\.

   1. In the **Greengrass service role** section, find **Current service role** to see whether a Greengrass service role is associated\.

      If you have a Greengrass service role associated, you meet this requirement to use the IP detector component\. Skip to [Configure the AWS IoT thing policy](#configure-iot-policy-requirement)\.

1. If the Greengrass service role isn't associated with AWS IoT Greengrass for your AWS account in this Region, create a Greengrass service role and associate it\. Do the following:

   1. Navigate to the [IAM console](https://console.aws.amazon.com/iam)\.

   1. Choose **Roles**\.

   1. Choose **Create role**\.

   1. On the **Create role** page, do the following:

      1. Under **Trusted entity type**, choose **AWS service**\.

      1. Under **Use case**, **Use cases for other AWS services**, choose **Greengrass**, select **Greengrass**\. This option specifies to add AWS IoT Greengrass as a trusted entity that can assume this role\.

      1. Choose **Next**\.

      1. Under **Permissions policies**, select the **AWSGreengrassResourceAccessRolePolicy** to attach to the role\.

      1. Choose **Next**\.

      1. In **Role name**, enter a name for the role, such as **Greengrass\_ServiceRole**\.

      1. Choose **Create role**\.

   1. <a name="open-iot-console"></a>Navigate to the [AWS IoT console](https://console.aws.amazon.com/iot)\.

   1. In the navigation pane, choose **Settings**\.

   1. In the **Greengrass service role** section, choose **Attach role**\.

   1. In the **Update Greengrass service role** modal, select the IAM role that you created, and then choose **Attach role**\.

#### Configure the Greengrass service role \(AWS CLI\)<a name="configure-service-role-requirement-cli"></a>

1. Check if the Greengrass service role is associated with AWS IoT Greengrass for your AWS account in this Region\.

   ```
   aws greengrassv2 get-service-role-for-account
   ```

   If the Greengrass service role is associated, the operation returns a response that contains information about the role\.

   If you have a Greengrass service role associated, you meet this requirement to use the IP detector component\. Skip to [Configure the AWS IoT thing policy](#configure-iot-policy-requirement)\.

1. If the Greengrass service role isn't associated with AWS IoT Greengrass for your AWS account in this Region, create a Greengrass service role and associate it\. Do the following:

   1. <a name="create-greengrass-service-role-step-create-role"></a>Create a role with a trust policy that allows AWS IoT Greengrass to assume the role\. This example creates a role named `Greengrass_ServiceRole`, but you can use a different name\. We recommend that you also include the `aws:SourceArn` and `aws:SourceAccount` global condition context keys in your trust policy to help prevent the *confused deputy* security problem\. The condition context keys restrict access to allow only those requests that come from the specified account and Greengrass workspace\. For more information about the confused deputy problem, see [Cross\-service confused deputy prevention](cross-service-confused-deputy-prevention.md)\.

------
#### [ Linux or Unix ]

      ```
      aws iam create-role --role-name Greengrass_ServiceRole --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "greengrass.amazonaws.com"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
              "ArnLike": {
                "aws:SourceArn": "arn:aws:greengrass:region:account-id:*"
              },
              "StringEquals": {
                "aws:SourceAccount": "account-id"
              }
            }
          }
        ]
      }'
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      aws iam create-role --role-name Greengrass_ServiceRole --assume-role-policy-document "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"greengrass.amazonaws.com\"},\"Action\":\"sts:AssumeRole\",\"Condition\":{\"ArnLike\":{\"aws:SourceArn\":\"arn:aws:greengrass:region:account-id:*\"},\"StringEquals\":{\"aws:SourceAccount\":\"account-id\"}}}]}"
      ```

------
#### [ PowerShell ]

      ```
      aws iam create-role --role-name Greengrass_ServiceRole --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "greengrass.amazonaws.com"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
              "ArnLike": {
                "aws:SourceArn": "arn:aws:greengrass:region:account-id:*"
              },
              "StringEquals": {
                "aws:SourceAccount": "account-id"
              }
            }
          }
        ]
      }'
      ```

------

   1. <a name="create-greengrass-service-role-step-copy-role-arn"></a>Copy the role ARN from the role metadata in the output\. You use the ARN to associate the role with your account\.

   1. <a name="create-greengrass-service-role-step-attach-policy"></a>Attach the `AWSGreengrassResourceAccessRolePolicy` policy to the role\.

      ```
      aws iam attach-role-policy --role-name Greengrass_ServiceRole --policy-arn arn:aws:iam::aws:policy/service-role/AWSGreengrassResourceAccessRolePolicy
      ```

   1. Associate the Greengrass service role with AWS IoT Greengrass for your AWS account\. Replace *role\-arn* with the ARN of the service role\.

      ```
      aws greengrassv2 associate-service-role-to-account --role-arn role-arn
      ```

      The operation returns the following response if it succeeds\.

      ```
      {
        "associatedAt": "timestamp"
      }
      ```

### Configure the AWS IoT thing policy<a name="configure-iot-policy-requirement"></a>

Core devices use X\.509 device certificates to authorize connections to AWS\. You attach AWS IoT policies to device certificates to define the permissions for a core device\. For more information, see [AWS IoT policies for data plane operations](device-auth.md#iot-policies) and [Minimal AWS IoT policy to support client devices](device-auth.md#client-device-support-minimal-iot-policy)\.

To connect client devices to a core device, the core device's AWS IoT policy must allow the following permissions:<a name="core-device-iot-policy-client-device-permissions"></a>
+ <a name="core-device-iot-policy-client-device-permissions-putcertificateauthorities"></a>`greengrass:PutCertificateAuthorities`
+ <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceidentity"></a>`greengrass:VerifyClientDeviceIdentity`
+ <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceiotcertificateassociation"></a>`greengrass:VerifyClientDeviceIoTCertificateAssociation`
+ <a name="core-device-iot-policy-client-device-permissions-getconnectivityinfo"></a>`greengrass:GetConnectivityInfo`
+ <a name="core-device-iot-policy-client-device-permissions-updateconnectivityinfo"></a>`greengrass:UpdateConnectivityInfo` – \(Optional\) This permission is required to use the [IP detector component](ip-detector-component.md), which reports the core device's network connectivity information to the AWS IoT Greengrass cloud service\.
+ <a name="core-device-iot-policy-client-device-permissions-shadows"></a>`iot:GetThingShadow`, `iot:UpdateThingShadow`, and `iot:DeleteThingShadow` – \(Optional\) These permissions are required to use the [shadow manager component](shadow-manager-component.md) to sync client device shadows with AWS IoT Core\. This feature requires [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later, shadow manager v2\.2\.0 or later, and [MQTT bridge](mqtt-bridge-component.md) v2\.2\.0 or later\.

In this section, you review the AWS IoT policies for your core device and add any required permissions that are missing\. If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), your core device has an AWS IoT policy that allows access to all AWS IoT Greengrass actions \(`greengrass:*`\)\. In this case, you must update the AWS IoT policy only if you plan to deploy the shadow manager component to sync device shadows with AWS IoT Core\. Otherwise, you can skip this section\.

#### Configure the AWS IoT thing policy \(console\)<a name="configure-iot-policy-requirement-console"></a>

1. <a name="update-iot-policy-console-open-greengrass-console"></a>In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Core devices**\.

1. <a name="update-iot-policy-console-choose-core-device"></a>On the **Core devices** page, choose the core device to update\.

1. <a name="update-iot-policy-console-choose-core-device-thing"></a>On the core device details page, choose the link to the core device's **Thing**\. This link opens the thing details page in the AWS IoT console\.

1. <a name="update-iot-policy-console-choose-thing-security"></a>On the thing details page, choose **Certificates**\.

1. <a name="update-iot-policy-console-choose-thing-certificate"></a>In the **Certificates** tab, choose the thing's active certificate\.

1. <a name="update-iot-policy-console-choose-certificate-policies"></a>On the certificate details page, choose **Policies**\.

1. <a name="update-iot-policy-console-choose-policy"></a>In the **Policies** tab, choose the AWS IoT policy to review and update\. You can add the required permissions to any policy that is attached to the core device's active certificate\.
**Note**  <a name="quick-installation-iot-policies-note"></a>
If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), you have two AWS IoT policies\. We recommend that you choose the policy named **GreengrassV2IoTThingPolicy**, if it exists\. Core devices that you create with the quick installer use this policy name by default\. If you add permissions to this policy, you are also granting these permissions to other core devices that use this policy\.

1. <a name="update-iot-policy-console-edit-policy"></a>In the policy overview, choose **Edit active version**\.

1. Review the policy for the required permissions, and add any required permissions that are missing\.<a name="core-device-iot-policy-client-device-permissions"></a>
   + <a name="core-device-iot-policy-client-device-permissions-putcertificateauthorities"></a>`greengrass:PutCertificateAuthorities`
   + <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceidentity"></a>`greengrass:VerifyClientDeviceIdentity`
   + <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceiotcertificateassociation"></a>`greengrass:VerifyClientDeviceIoTCertificateAssociation`
   + <a name="core-device-iot-policy-client-device-permissions-getconnectivityinfo"></a>`greengrass:GetConnectivityInfo`
   + <a name="core-device-iot-policy-client-device-permissions-updateconnectivityinfo"></a>`greengrass:UpdateConnectivityInfo` – \(Optional\) This permission is required to use the [IP detector component](ip-detector-component.md), which reports the core device's network connectivity information to the AWS IoT Greengrass cloud service\.
   + <a name="core-device-iot-policy-client-device-permissions-shadows"></a>`iot:GetThingShadow`, `iot:UpdateThingShadow`, and `iot:DeleteThingShadow` – \(Optional\) These permissions are required to use the [shadow manager component](shadow-manager-component.md) to sync client device shadows with AWS IoT Core\. This feature requires [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later, shadow manager v2\.2\.0 or later, and [MQTT bridge](mqtt-bridge-component.md) v2\.2\.0 or later\.

1. \(Optional\) To allow the core device to sync shadows with AWS IoT Core, add the following statement to the policy\. If you plan to interact with client device shadows, but not sync them with AWS IoT Core, skip this step\. Replace *region* and *account\-id* with the Region that you use and your AWS account number\.
   + This example statement allows access to all things' device shadows\. To follow best security practices, you can restrict access to only the core device and the client devices that you connect to the core device\. For more information, see [Minimal AWS IoT policy to support client devices](device-auth.md#client-device-support-minimal-iot-policy)\.

   ```
   {
     "Effect": "Allow",
     "Action": [
       "iot:GetThingShadow",
       "iot:UpdateThingShadow",
       "iot:DeleteThingShadow"
     ],
     "Resource": [
       "arn:aws:iot:region:account-id:thing/*"
     ]
   }
   ```

   After you add this statement, the policy document might look similar to the following example\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "iot:Connect",
           "iot:Publish",
           "iot:Subscribe",
           "iot:Receive",
           "greengrass:*"
         ],
         "Resource": "*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "iot:GetThingShadow",
           "iot:UpdateThingShadow",
           "iot:DeleteThingShadow"
         ],
         "Resource": [
           "arn:aws:iot:region:account-id:thing/*"
         ]
       }
     ]
   }
   ```

1. <a name="update-iot-policy-console-set-as-active-version"></a>To set a new policy version as the active version, under **Policy version status**, select **Set the edited version as the active version for this policy**\.

1. <a name="update-iot-policy-console-save-policy"></a>Choose **Save as new version**\.

#### Configure the AWS IoT thing policy \(AWS CLI\)<a name="configure-iot-policy-requirement-cli"></a>

1. <a name="update-iot-policy-cli-list-thing-principals"></a>List the principals for the core device's AWS IoT thing\. Thing principals can be X\.509 device certificates or other identifies\. Run the following command, and replace *MyGreengrassCore* with the name of the core device\.

   ```
   aws iot list-thing-principals --thing-name MyGreengrassCore
   ```

   The operation returns a response that lists the core device's thing principals\.

   ```
   {
       "principals": [
           "arn:aws:iot:us-west-2:123456789012:cert/certificateId"
       ]
   }
   ```

1. <a name="update-iot-policy-cli-identify-active-certificate"></a>Identify the core device's active certificate\. Run the following command, and replace *certificateId* with the ID of each certificate from the previous step until you find the active certificate\. The certificate ID is the hexadecimal string at the end of the certificate ARN\. The `--query` argument specifies to output only the certificate's status\.

   ```
   aws iot describe-certificate --certificate-id certificateId --query 'certificateDescription.status'
   ```

   The operation returns the certificate status as a string\. For example, if the certificate is active, this operation outputs `"ACTIVE"`\.

1. <a name="update-iot-policy-cli-list-certificate-policies"></a>List the AWS IoT policies that are attached to the certificate\. Run the following command, and replace the certificate ARN with the ARN of the certificate\.

   ```
   aws iot list-principal-policies --principal arn:aws:iot:us-west-2:123456789012:cert/certificateId
   ```

   The operation returns a response that lists the AWS IoT policies that are attached to the certificate\.

   ```
   {
       "policies": [
           {
               "policyName": "GreengrassTESCertificatePolicyMyGreengrassCoreTokenExchangeRoleAlias",
               "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/GreengrassTESCertificatePolicyMyGreengrassCoreTokenExchangeRoleAlias"
           },
           {
               "policyName": "GreengrassV2IoTThingPolicy",
               "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/GreengrassV2IoTThingPolicy"
           }
       ]
   }
   ```

1. <a name="update-iot-policy-cli-choose-policy"></a>Choose the policy to view and update\.
**Note**  <a name="quick-installation-iot-policies-note"></a>
If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), you have two AWS IoT policies\. We recommend that you choose the policy named **GreengrassV2IoTThingPolicy**, if it exists\. Core devices that you create with the quick installer use this policy name by default\. If you add permissions to this policy, you are also granting these permissions to other core devices that use this policy\.

1. <a name="update-iot-policy-cli-get-policy-document"></a>Get the policy's document\. Run the following command, and replace *GreengrassV2IoTThingPolicy* with the name of the policy\.

   ```
   aws iot get-policy --policy-name GreengrassV2IoTThingPolicy
   ```

   The operation returns a response that contains the policy's document and other information about the policy\. The policy document is a JSON object serialized as a string\. 

   ```
   {
       "policyName": "GreengrassV2IoTThingPolicy",
       "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/GreengrassV2IoTThingPolicy",
       "policyDocument": "{\n  \"Version\": \"2012-10-17\",\n  \"Statement\": [\n    {\n      \"Effect\": \"Allow\",\n      \"Action\": [\n                \"iot:Connect\",\n                \"iot:Publish\",\n                \"iot:Subscribe\",\n                \"iot:Receive\",\n                \"greengrass:*\"\n],\n      \"Resource\": \"*\"\n    }\n  ]\n}",
       "defaultVersionId": "1",
       "creationDate": "2021-02-05T16:03:14.098000-08:00",
       "lastModifiedDate": "2021-02-05T16:03:14.098000-08:00",
       "generationId": "f19144b798534f52c619d44f771a354f1b957dfa2b850625d9f1d0fde530e75f"
   }
   ```

1. <a name="update-iot-policy-cli-create-policy-document-file"></a>Use an online converter or other tool to convert the policy document string to a JSON object, and then save it to a file named `iot-policy.json`\.

   For example, if you have the [jq](https://stedolan.github.io/jq/) tool installed, you can run the following command to get the policy document, convert it to a JSON object, and save the policy document as a JSON object\.

   ```
   aws iot get-policy --policy-name GreengrassV2IoTThingPolicy --query 'policyDocument' | jq fromjson >> iot-policy.json
   ```

1. Review the policy for the required permissions, and add any required permissions that are missing\.

   <a name="nano-command-intro-existing-file"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to open the file\.

   ```
   nano iot-policy.json
   ```<a name="core-device-iot-policy-client-device-permissions"></a>
   + <a name="core-device-iot-policy-client-device-permissions-putcertificateauthorities"></a>`greengrass:PutCertificateAuthorities`
   + <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceidentity"></a>`greengrass:VerifyClientDeviceIdentity`
   + <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceiotcertificateassociation"></a>`greengrass:VerifyClientDeviceIoTCertificateAssociation`
   + <a name="core-device-iot-policy-client-device-permissions-getconnectivityinfo"></a>`greengrass:GetConnectivityInfo`
   + <a name="core-device-iot-policy-client-device-permissions-updateconnectivityinfo"></a>`greengrass:UpdateConnectivityInfo` – \(Optional\) This permission is required to use the [IP detector component](ip-detector-component.md), which reports the core device's network connectivity information to the AWS IoT Greengrass cloud service\.
   + <a name="core-device-iot-policy-client-device-permissions-shadows"></a>`iot:GetThingShadow`, `iot:UpdateThingShadow`, and `iot:DeleteThingShadow` – \(Optional\) These permissions are required to use the [shadow manager component](shadow-manager-component.md) to sync client device shadows with AWS IoT Core\. This feature requires [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later, shadow manager v2\.2\.0 or later, and [MQTT bridge](mqtt-bridge-component.md) v2\.2\.0 or later\.

1. <a name="update-iot-policy-cli-create-policy-version"></a>Save the changes as a new version of the policy\. Run the following command, and replace *GreengrassV2IoTThingPolicy* with the name of the policy\.

   ```
   aws iot create-policy-version --policy-name GreengrassV2IoTThingPolicy --policy-document file://iot-policy.json --set-as-default
   ```

   The operation returns a response similar to the following example if it succeeds\.

   ```
   {
       "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/GreengrassV2IoTThingPolicy",
       "policyDocument": "{\n  \"Version\": \"2012-10-17\",\n  \"Statement\": [\n    {\n      \"Effect\": \"Allow\",\n      \"Action\": [\n\t\t\"iot:Connect\",\n\t\t\"iot:Publish\",\n\t\t\"iot:Subscribe\",\n\t\t\"iot:Receive\",\n\t\t\"greengrass:*\"\n      ],\n      \"Resource\": \"*\"\n    }\n  ]\n}",
       "policyVersionId": "2",
       "isDefaultVersion": true
   }
   ```

## Greengrass components for client device support<a name="cloud-discovery-components"></a>

**Important**  <a name="client-device-support-nucleus-requirement"></a>
The core device must run [Greengrass nucleus](greengrass-nucleus-component.md) v2\.2\.0 or later to support client devices\.

To enable client devices to connect and communicate with a core device, you deploy the following Greengrass components to the core device:
+ <a name="client-device-component-overview-client-device-auth"></a>[Client device auth](client-device-auth-component.md) \(`aws.greengrass.clientdevices.Auth`\)

  Deploy the client device auth component to authenticate client devices and authorize client device actions\. This component allows your AWS IoT things to connect to a core device\.

  This component requires some configuration to use it\. You must specify groups of client devices and the operations that each group is authorized to perform, such as to connect and communicate over MQTT\. For more information, see [client device auth component configuration](client-device-auth-component.md#client-device-auth-component-configuration)\.
+ <a name="client-device-component-overview-mqtt-broker-moquette"></a>[MQTT 3\.1\.1 broker \(Moquette\)](mqtt-broker-moquette-component.md) \(`aws.greengrass.clientdevices.mqtt.Moquette`\)

  Deploy the Moquette MQTT broker component to run a lightweight MQTT broker\. The Moquette MQTT broker is compliant with MQTT 3\.1\.1 and includes local support for QoS 0, QoS 1, QoS 2, retained messages, last will messages, and persistent subscriptions\.

  You aren't required to configure this component to use it\. However, you can configure the port where this component operates the MQTT broker\. By default, it uses port 8883\.
+ <a name="client-device-component-overview-mqtt-broker-emqx"></a>[MQTT 5 broker \(EMQX\)](mqtt-broker-emqx-component.md) \(`aws.greengrass.clientdevices.mqtt.EMQX`\)
**Note**  
To use the EMQX MQTT 5 broker, you must use [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later and client device auth v2\.2\.0 or later\.

  Deploy the EMQX MQTT broker component to use MQTT 5\.0 features in communication between client devices and the core device\. The EMQX MQTT broker is compliant with MQTT 5\.0 and includes support for session and message expiration intervals, user properties, shared subscriptions, topic aliases, and more\.

  You aren't required to configure this component to use it\. However, you can configure the port where this component operates the MQTT broker\. By default, it uses port 8883\.
+ <a name="client-device-component-overview-mqtt-bridge"></a>[MQTT bridge](mqtt-bridge-component.md) \(`aws.greengrass.clientdevices.mqtt.Bridge`\)

  \(Optional\) Deploy the MQTT bridge component to relay messages between client devices \(local MQTT\), local publish/subscribe, and AWS IoT Core MQTT\. Configure this component to sync client devices with AWS IoT Core and interact with client devices from Greengrass components\.

  This component requires configuration to use\. You must specify the topic mappings where this component relays messages\. For more information, see [MQTT bridge component configuration](mqtt-bridge-component.md#mqtt-bridge-component-configuration)\.
+ <a name="client-device-component-overview-ip-detector"></a>[IP detector](ip-detector-component.md) \(`aws.greengrass.clientdevices.IPDetector`\)

  \(Optional\) Deploy the IP detector component to automatically report the core device's MQTT broker endpoints to the AWS IoT Greengrass cloud service\. You cannot use this component if you have a complex network setup, such as one where a router forwards the MQTT broker port to the core device\.

  You aren't required to configure this component to use it\.
+ <a name="client-device-component-overview-shadow-manager"></a>[Shadow manager](shadow-manager-component.md) \(`aws.greengrass.ShadowManager`\)
**Note**  
To manage client device shadows, you must use [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later, shadow manager v2\.2\.0 or later, and [MQTT bridge](mqtt-bridge-component.md) v2\.2\.0 or later\.

  \(Optional\) Deploy the shadow manager component to manage client device shadows on the core device\. Greengrass components can get, update, and delete client device shadows to interact with client devices\. You can also configure the shadow manager component to synchronize client device shadows with the AWS IoT Core cloud service\.

  To use this component with client device shadows, you must configure the MQTT bridge component to relay messages between client devices and shadow manager, which uses local publish/subscribe\. Otherwise, this component doesn't require configuration to use, but it does require configuration to sync device shadows\.

**Note**  <a name="note-deploy-one-mqtt-broker"></a>
We recommend that you deploy only one MQTT broker component\. The [MQTT bridge](mqtt-bridge-component.md) and [IP detector](ip-detector-component.md) components work with only one MQTT broker component at a time\. If you deploy multiple MQTT broker components, you must configure them to use different ports\.

## Configure cloud discovery \(console\)<a name="configure-cloud-discovery-console"></a>

You can use the AWS IoT Greengrass console to associate client devices, manage core device endpoints, and deploy components to enable client device support\. For more information, see [Step 2: Enable client device support](client-devices-tutorial.md#enable-client-device-support)\.

## Configure cloud discovery \(AWS CLI\)<a name="configure-cloud-discovery-cli"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to associate client devices, manage core device endpoints, and deploy components to enable client device support\. For more information, see the following:
+ [Manage client device associations \(AWS CLI\)](associate-client-devices.md#manage-client-device-associations-cli)
+ [Manage core device endpoints](manage-core-device-endpoints.md)
+ [AWS\-provided client device components](client-device-components.md)
+ [Create deployments](create-deployments.md)
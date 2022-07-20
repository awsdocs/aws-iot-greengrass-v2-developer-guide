# Set up AWS IoT fleet provisioning for Greengrass core devices<a name="fleet-provisioning-setup"></a>

To [install the AWS IoT Greengrass Core software with fleet provisioning](fleet-provisioning.md), you must first set up the following resources in your AWS account\. These resources enable devices to register themselves with AWS IoT and operate as Greengrass core devices\. Follow steps in this section once to create and configure these resources in your AWS account\.
+ A token exchange IAM role, which core devices use to authorize calls to AWS services\.
+ An AWS IoT role alias that points to the token exchange role\.
+ \(Optional\) An AWS IoT policy, which core devices use to authorize calls to the AWS IoT and AWS IoT Greengrass services\. This AWS IoT policy must allow the `iot:AssumeRoleWithCertificate` permission for the AWS IoT role alias that points to the token exchange role\.

  You can use a single AWS IoT policy for all core devices in your fleet, or you can configure your fleet provisioning template to create an AWS IoT policy for each core device\.
+ An AWS IoT fleet provisioning template\. This template must specify the following:<a name="installation-fleet-provisioning-template-requirements"></a>
  + An AWS IoT thing resource\. You can specify a list of existing thing groups to deploy components to each device when it comes online\.
  + An AWS IoT policy resource\. This resource can define one of the following properties:
    + The name of an existing AWS IoT policy\. If you choose this option, the core devices that you create from this template use the same AWS IoT policy, and you can manage their permissions as a fleet\.
    + An AWS IoT policy document\. If you choose this option, each core device that you create from this template uses a unique AWS IoT policy, and you can manage permissions for each individual core device\.
  + An AWS IoT certificate resource\. This certificate resource must use the `AWS::IoT::Certificate::Id` parameter to attach the certificate to the core device\. For more information, see [Just\-in\-time provisioning](https://docs.aws.amazon.com/iot/latest/developerguide/jit-provisioning.html) in the *AWS IoT Developer Guide*\.
+ An AWS IoT provisioning claim certificate and private key for the fleet provisioning template\. You can embed this certificate and private key in devices during manufacturing, so the devices can register and provision themselves when they come online\.
**Important**  <a name="installation-fleet-provisioning-secure-claim-private-keys"></a>
Provisioning claim private keys should be secured at all times, including on Greengrass core devices\. We recommend that you use Amazon CloudWatch metrics and logs to monitor for indications of misuse, such as unauthorized use of the claim certificate to provision devices\. If you detect misuse, disable the provisioning claim certificate so that it can't be used for device provisioning\. For more information, see [Monitoring AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/monitoring_overview.html) in the *AWS IoT Core Developer Guide*\.  
To help you better manage the number of devices, and which devices, that register themselves in your AWS account, you can specify a pre\-provisioning hook when you create a fleet provisioning template\. A pre\-provisioning hook is an AWS Lambda function that validates template parameters that devices provide during registration\. For example, you might create a pre\-provisioning hook that checks a device ID against a database to verify that the device has permission to provision\. For more information, see [Pre\-provisioning hooks](https://docs.aws.amazon.com/iot/latest/developerguide/pre-provisioning-hook.html) in the *AWS IoT Core Developer Guide*\.
+ An AWS IoT policy that you attach to the provisioning claim certificate to allow devices to register and use the fleet provisioning template\.

**Topics**
+ [Create a token exchange role](#create-token-exchange-role)
+ [Create an AWS IoT policy](#create-iot-policy)
+ [Create a fleet provisioning template](#create-provisioning-template)
+ [Create a provisioning claim certificate and private key](#create-claim-certificates)

## Create a token exchange role<a name="create-token-exchange-role"></a>

<a name="installation-create-token-exchange-role-intro"></a>Greengrass core devices use an IAM service role, called the *token exchange role*, to authorize calls to AWS services\. The device uses the AWS IoT credentials provider to get temporary AWS credentials for this role, which allows the device to interact with AWS IoT, send logs to Amazon CloudWatch Logs, and download custom component artifacts from Amazon S3\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

<a name="installation-create-token-exchange-role-alias-intro"></a>You use an AWS IoT *role alias* to configure the token exchange role for Greengrass core devices\. Role aliases enable you to change the token exchange role for a device but keep the device configuration the same\. For more information, see [Authorizing direct calls to AWS services](https://docs.aws.amazon.com/iot/latest/developerguide/authorizing-direct-aws.html) in the *AWS IoT Core Developer Guide*\.

In this section, you create a token exchange IAM role and an AWS IoT role alias that points to the role\. If you have already set up a Greengrass core device, you can use its token exchange role and role alias instead of creating new ones\.

**To create a token exchange IAM role**

1. <a name="create-token-exchange-role-create-iam-role"></a>Create an IAM role that your device can use as a token exchange role\. Do the following:

   1. Create a file that contains the trust policy document that the token exchange role requires\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano device-role-trust-policy.json
      ```

      Copy the following JSON into the file\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "credentials.iot.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
          }
        ]
      }
      ```

   1. Create the token exchange role with the trust policy document\.
      + Replace *GreengrassV2TokenExchangeRole* with the name of the IAM role to create\.

      ```
      aws iam create-role --role-name GreengrassV2TokenExchangeRole --assume-role-policy-document file://device-role-trust-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "Role": {
          "Path": "/",
          "RoleName": "GreengrassV2TokenExchangeRole",
          "RoleId": "AROAZ2YMUHYHK5OKM77FB",
          "Arn": "arn:aws:iam::123456789012:role/GreengrassV2TokenExchangeRole",
          "CreateDate": "2021-02-06T00:13:29+00:00",
          "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
              {
                "Effect": "Allow",
                "Principal": {
                  "Service": "credentials.iot.amazonaws.com"
                },
                "Action": "sts:AssumeRole"
              }
            ]
          }
        }
      ```

   1. Create a file that contains the access policy document that the token exchange role requires\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano device-role-access-policy.json
      ```

      Copy the following JSON into the file\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "logs:CreateLogGroup",
              "logs:CreateLogStream",
              "logs:PutLogEvents",
              "logs:DescribeLogStreams",
              "s3:GetBucketLocation"
            ],
            "Resource": "*"
          }
        ]
      }
      ```
**Note**  
This access policy doesn't allow access to component artifacts in S3 buckets\. To deploy custom components that define artifacts in Amazon S3, you must add permissions to the role to allow your core device to retrieve component artifacts\. For more information, see [Allow access to S3 buckets for component artifacts](device-service-role.md#device-service-role-access-s3-bucket)\.  
If you don't yet have an S3 bucket for component artifacts, you can add these permissions later after you create a bucket\.

   1. Create the IAM policy from the policy document\.
      + Replace *GreengrassV2TokenExchangeRoleAccess* with the name of the IAM policy to create\.

      ```
      aws iam create-policy --policy-name GreengrassV2TokenExchangeRoleAccess --policy-document file://device-role-access-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "Policy": {
          "PolicyName": "GreengrassV2TokenExchangeRoleAccess",
          "PolicyId": "ANPAZ2YMUHYHACI7C5Z66",
          "Arn": "arn:aws:iam::123456789012:policy/GreengrassV2TokenExchangeRoleAccess",
          "Path": "/",
          "DefaultVersionId": "v1",
          "AttachmentCount": 0,
          "PermissionsBoundaryUsageCount": 0,
          "IsAttachable": true,
          "CreateDate": "2021-02-06T00:37:17+00:00",
          "UpdateDate": "2021-02-06T00:37:17+00:00"
        }
      }
      ```

   1. Attach the IAM policy to the token exchange role\.
      + Replace *GreengrassV2TokenExchangeRole* with the name of the IAM role\.
      + Replace the policy ARN with the ARN of the IAM policy that you created in the previous step\.

      ```
      aws iam attach-role-policy --role-name GreengrassV2TokenExchangeRole --policy-arn arn:aws:iam::123456789012:policy/GreengrassV2TokenExchangeRoleAccess
      ```

      The command doesn't have any output if the request succeeds\.

1. <a name="create-token-exchange-role-create-iot-role-alias"></a>Create an AWS IoT role alias that points to the token exchange role\.
   + Replace *GreengrassCoreTokenExchangeRoleAlias* with the name of the role alias to create\.
   + Replace the role ARN with the ARN of the IAM role that you created in the previous step\.

   ```
   aws iot create-role-alias --role-alias GreengrassCoreTokenExchangeRoleAlias --role-arn arn:aws:iam::123456789012:role/GreengrassV2TokenExchangeRole
   ```

   The response looks similar to the following example, if the request succeeds\.

   ```
   {
     "roleAlias": "GreengrassCoreTokenExchangeRoleAlias",
     "roleAliasArn": "arn:aws:iot:us-west-2:123456789012:rolealias/GreengrassCoreTokenExchangeRoleAlias"
   }
   ```
**Note**  
To create a role alias, you must have permission to pass the token exchange IAM role to AWS IoT\. If you receive an error message when you try to create a role alias, check that your AWS user has this permission\. For more information, see [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *AWS Identity and Access Management User Guide*\.

## Create an AWS IoT policy<a name="create-iot-policy"></a>

After you register a device as an AWS IoT thing, that device can use a digital certificate to authenticate with AWS\. This certificate includes one or more AWS IoT policies that define the permissions that a device can use with the certificate\. These policies allow the device to communicate with AWS IoT and AWS IoT Greengrass\.

With AWS IoT fleet provisioning, devices connect to AWS IoT to create and download a device certificate\. In the fleet provisioning template that you create in the next section, you can specify whether AWS IoT attaches the same AWS IoT policy to all devices' certificates, or creates a new policy for each device\.

In this section, you create an AWS IoT policy that AWS IoT attaches to all devices' certificates\. With this approach, you can manage permissions for all devices as a fleet\. If you would rather create a new AWS IoT policy for each device, you can skip this section, and refer to the policy in it when you define your fleet template\.

**To create an AWS IoT policy**
+ Create an AWS IoT policy that defines the AWS IoT permissions for your fleet of Greengrass core devices\. The following policy allows access to all MQTT topics and Greengrass operations, so your device works with custom applications and future changes that require new Greengrass operations\. This policy also allows the `iot:AssumeRoleWithCertificate` permission, which allows your devices to use the token exchange role that you created in the previous section\. You can restrict this policy down based on your use case\. For more information, see [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy)\.

  Do the following:

  1. Create a file that contains the AWS IoT policy document that Greengrass core devices require\.

     <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

     ```
     nano greengrass-v2-iot-policy.json
     ```

     Copy the following JSON into the file\.
     + Replace the `iot:AssumeRoleWithCertificate` resource with the ARN of the AWS IoT role alias that you created in the previous section\.

     ```
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Action": [
             "iot:Publish",
             "iot:Subscribe",
             "iot:Receive",
             "iot:Connect",
             "greengrass:*"
           ],
           "Resource": [
             "*"
           ]
         },
         {
           "Effect": "Allow",
           "Action": "iot:AssumeRoleWithCertificate",
           "Resource": "arn:aws:iot:us-west-2:123456789012:rolealias/GreengrassCoreTokenExchangeRoleAlias"
         }
       ]
     }
     ```

  1. Create an AWS IoT policy from the policy document\.
     + Replace *GreengrassV2IoTThingPolicy* with the name of the policy to create\.

     ```
     aws iot create-policy --policy-name GreengrassV2IoTThingPolicy --policy-document file://greengrass-v2-iot-policy.json
     ```

     The response looks similar to the following example, if the request succeeds\.

     ```
     {
       "policyName": "GreengrassV2IoTThingPolicy",
       "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/GreengrassV2IoTThingPolicy",
       "policyDocument": "{
         \"Version\": \"2012-10-17\",
         \"Statement\": [
           {
             \"Effect\": \"Allow\",
             \"Action\": [
               \"iot:Publish\",
               \"iot:Subscribe\",
               \"iot:Receive\",
               \"iot:Connect\",
               \"greengrass:*\"
             ],
             \"Resource\": [
               \"*\"
             ]
           },
           {
             \"Effect\": \"Allow\",
             \"Action\": \"iot:AssumeRoleWithCertificate\",
             \"Resource\": \"arn:aws:iot:us-west-2:123456789012:rolealias/GreengrassCoreTokenExchangeRoleAlias\"
           }
         ]
       }",
       "policyVersionId": "1"
     }
     ```

## Create a fleet provisioning template<a name="create-provisioning-template"></a>

AWS IoT fleet provisioning templates define how to provision AWS IoT things, policies, and certificates\. To provision Greengrass core devices with the fleet provisioning plugin, you must create a template that specifies that following:<a name="installation-fleet-provisioning-template-requirements"></a>
+ An AWS IoT thing resource\. You can specify a list of existing thing groups to deploy components to each device when it comes online\.
+ An AWS IoT policy resource\. This resource can define one of the following properties:
  + The name of an existing AWS IoT policy\. If you choose this option, the core devices that you create from this template use the same AWS IoT policy, and you can manage their permissions as a fleet\.
  + An AWS IoT policy document\. If you choose this option, each core device that you create from this template uses a unique AWS IoT policy, and you can manage permissions for each individual core device\.
+ An AWS IoT certificate resource\. This certificate resource must use the `AWS::IoT::Certificate::Id` parameter to attach the certificate to the core device\. For more information, see [Just\-in\-time provisioning](https://docs.aws.amazon.com/iot/latest/developerguide/jit-provisioning.html) in the *AWS IoT Developer Guide*\.

In the template, you can specify to add the AWS IoT thing to a list of existing thing groups\. When the core device connects to AWS IoT Greengrass for the first time, it receives Greengrass deployments for each thing group where it's a member\. You can use thing groups to deploy the latest software to each device as soon as it comes online\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

The AWS IoT service requires permissions to create and update AWS IoT resources in your AWS account when provisioning devices\. To give the AWS IoT service access, you create an IAM role and provide it when you create the template\. AWS IoT provides an managed policy, [AWSIoTThingsRegistration](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AWSIoTThingsRegistration), that allows access to all permissions that AWS IoT might use when provisioning devices\. You can use this managed policy, or create a custom policy that scopes down the permissions in the managed policy for your use case\.

In this section, you create an IAM role that allows AWS IoT to provision resources for devices, and you create a fleet provisioning template that uses that IAM role\.

**To create a fleet provisioning template**

1. Create an IAM role that AWS IoT can assume to provision resources in your AWS account\. Do the following:

   1. Create a file that contains the trust policy document that allows AWS IoT to assume the role\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano aws-iot-trust-policy.json
      ```

      Copy the following JSON into the file\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "iot.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
          }
        ]
      }
      ```

   1. Create an IAM role with the trust policy document\.
      + Replace *GreengrassFleetProvisioningRole* with the name of the IAM role to create\.

      ```
      aws iam create-role --role-name GreengrassFleetProvisioningRole --assume-role-policy-document file://aws-iot-trust-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "Role": {
          "Path": "/",
          "RoleName": "GreengrassFleetProvisioningRole",
          "RoleId": "AROAZ2YMUHYHK5OKM77FB",
          "Arn": "arn:aws:iam::123456789012:role/GreengrassFleetProvisioningRole",
          "CreateDate": "2021-07-26T00:15:12+00:00",
          "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
              {
                "Effect": "Allow",
                "Principal": {
                  "Service": "iot.amazonaws.com"
                },
                "Action": "sts:AssumeRole"
              }
            ]
          }
        }
      }
      ```

   1. Review the [AWSIoTThingsRegistration](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AWSIoTThingsRegistration) policy, which allows access to all permissions that AWS IoT might use when provisioning devices\. You can use this managed policy, or create a custom policy that defines scoped\-down permissions for your use case\. If you choose to create a custom policy, do so now\.

   1. Attach the IAM policy to the fleet provisioning role\.
      + Replace *GreengrassFleetProvisioningRole* with the name of the IAM role\.
      + If you created a custom policy in the previous step, replace the policy ARN with the ARN of the IAM policy to use\.

      ```
      aws iam attach-role-policy --role-name GreengrassFleetProvisioningRole --policy-arn arn:aws:iam::aws:policy/service-role/AWSIoTThingsRegistration
      ```

      The command doesn't have any output if the request succeeds\.

1. \(Optional\) Create a *pre\-provisioning hook*, which is an AWS Lambda function that validates template parameters that devices provide during registration\. You can use a pre\-provisioning hook to gain more control over which and how many devices onboard in your AWS account\. For more information, see [Pre\-provisioning hooks](https://docs.aws.amazon.com/iot/latest/developerguide/pre-provisioning-hook.html) in the *AWS IoT Core Developer Guide*\.

1. Create a fleet provisioning template\. Do the following:

   1. Create a file to contain the provisioning template document\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano greengrass-fleet-provisioning-template.json
      ```

      Write the provisioning template document\. You can start from the following example provisioning template, which specifies to create an AWS IoT thing with the following properties:
      + The thing's name is the value that you specify in the `ThingName` template parameter\.
      + The thing is a member of the thing group that you specify in the `ThingGroupName` template parameter\. The thing group must exist in your AWS account\.
      + The thing's certificate has the AWS IoT policy named `GreengrassV2IoTThingPolicy` attached to it\.

      For more information, see [Provisioning templates](https://docs.aws.amazon.com/iot/latest/developerguide/provision-template.html) in the *AWS IoT Core Developer Guide*\.

      ```
      {
        "Parameters": {
          "ThingName": {
            "Type": "String"
          },
          "ThingGroupName": {
            "Type": "String"
          },
          "AWS::IoT::Certificate::Id": {
            "Type": "String"
          }
        },
        "Resources": {
          "MyThing": {
            "OverrideSettings": {
              "AttributePayload": "REPLACE",
              "ThingGroups": "REPLACE",
              "ThingTypeName": "REPLACE"
            },
            "Properties": {
              "AttributePayload": {},
              "ThingGroups": [
                {
                  "Ref": "ThingGroupName"
                }
              ],
              "ThingName": {
                "Ref": "ThingName"
              }
            },
            "Type": "AWS::IoT::Thing"
          },
          "MyPolicy": {
            "Properties": {
              "PolicyName": "GreengrassV2IoTThingPolicy"
            },
            "Type": "AWS::IoT::Policy"
          },
          "MyCertificate": {
            "Properties": {
              "CertificateId": {
                "Ref": "AWS::IoT::Certificate::Id"
              },
              "Status": "Active"
            },
            "Type": "AWS::IoT::Certificate"
          }
        }
      }
      ```
**Note**  
*MyThing*, *MyPolicy*, and *MyCertificate* are arbitrary names that identify each resource specification in the fleet provisioning template\. AWS IoT doesn't use these names in the resources that it creates from the template\. You can use these names or replace them with values that help you identify each resource in the template\.

   1. Create the fleet provisioning template from the provisioning template document\.
      + Replace *GreengrassFleetProvisioningTemplate* with the name of the template to create\.
      + Replace the template description with a description for your template\.
      + Replace the provisioning role ARN with the ARN of the role that you created earlier\.

------
#### [ Linux or Unix ]

      ```
      aws iot create-provisioning-template \
        --template-name GreengrassFleetProvisioningTemplate \
        --description "A provisioning template for Greengrass core devices." \
        --provisioning-role-arn "arn:aws:iam::123456789012:role/GreengrassFleetProvisioningRole" \
        --template-body file://greengrass-fleet-provisioning-template.json \
        --enabled
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      aws iot create-provisioning-template ^
        --template-name GreengrassFleetProvisioningTemplate ^
        --description "A provisioning template for Greengrass core devices." ^
        --provisioning-role-arn "arn:aws:iam::123456789012:role/GreengrassFleetProvisioningRole" ^
        --template-body file://greengrass-fleet-provisioning-template.json ^
        --enabled
      ```

------
#### [ PowerShell ]

      ```
      aws iot create-provisioning-template `
        --template-name GreengrassFleetProvisioningTemplate `
        --description "A provisioning template for Greengrass core devices." `
        --provisioning-role-arn "arn:aws:iam::123456789012:role/GreengrassFleetProvisioningRole" `
        --template-body file://greengrass-fleet-provisioning-template.json `
        --enabled
      ```

------
**Note**  
If you created a pre\-provisioning hook, specify the ARN of the pre\-provisioning hook's Lambda function with the `--pre-provisioning-hook` argument\.  

      ```
      --pre-provisioning-hook targetArn=arn:aws:lambda:us-west-2:123456789012:function:GreengrassPreProvisioningHook
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
          "templateArn": "arn:aws:iot:us-west-2:123456789012:provisioningtemplate/GreengrassFleetProvisioningTemplate",
          "templateName": "GreengrassFleetProvisioningTemplate",
          "defaultVersionId": 1
      }
      ```

## Create a provisioning claim certificate and private key<a name="create-claim-certificates"></a>

Claim certificates are X\.509 certificates that allow devices to register as AWS IoT things and retrieve a unique X\.509 device certificate to use for regular operations\. After you create a claim certificate, you attach an AWS IoT policy that allows devices to use it to create unique device certificates and provision with a fleet provisioning template\. Devices with the claim certificate can provision using only the provisioning template that you allow in the AWS IoT policy\.

In this section, you create the claim certificate and configure it for devices to use with the fleet provisioning template that you created in the previous section\.

**Important**  <a name="installation-fleet-provisioning-secure-claim-private-keys"></a>
Provisioning claim private keys should be secured at all times, including on Greengrass core devices\. We recommend that you use Amazon CloudWatch metrics and logs to monitor for indications of misuse, such as unauthorized use of the claim certificate to provision devices\. If you detect misuse, disable the provisioning claim certificate so that it can't be used for device provisioning\. For more information, see [Monitoring AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/monitoring_overview.html) in the *AWS IoT Core Developer Guide*\.  
To help you better manage the number of devices, and which devices, that register themselves in your AWS account, you can specify a pre\-provisioning hook when you create a fleet provisioning template\. A pre\-provisioning hook is an AWS Lambda function that validates template parameters that devices provide during registration\. For example, you might create a pre\-provisioning hook that checks a device ID against a database to verify that the device has permission to provision\. For more information, see [Pre\-provisioning hooks](https://docs.aws.amazon.com/iot/latest/developerguide/pre-provisioning-hook.html) in the *AWS IoT Core Developer Guide*\.

**To create a provisioning claim certificate and private key**

1. Create a folder where you download the claim certificate and private key\.

   ```
   mkdir claim-certs
   ```

1. Create and save a certificate and private key to use for provisioning\. AWS IoT provides client certificates that are signed by the Amazon Root certificate authority \(CA\)\.

------
#### [ Linux or Unix ]

   ```
   aws iot create-keys-and-certificate \
     --certificate-pem-outfile "claim-certs/claim.pem.crt" \
     --public-key-outfile "claim-certs/claim.public.pem.key" \
     --private-key-outfile "claim-certs/claim.private.pem.key" \
     --set-as-active
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws iot create-keys-and-certificate ^
     --certificate-pem-outfile "claim-certs/claim.pem.crt" ^
     --public-key-outfile "claim-certs/claim.public.pem.key" ^
     --private-key-outfile "claim-certs/claim.private.pem.key" ^
     --set-as-active
   ```

------
#### [ PowerShell ]

   ```
   aws iot create-keys-and-certificate `
     --certificate-pem-outfile "claim-certs/claim.pem.crt" `
     --public-key-outfile "claim-certs/claim.public.pem.key" `
     --private-key-outfile "claim-certs/claim.private.pem.key" `
     --set-as-active
   ```

------

   The response contains information about the certificate, if the request succeeds\. Save the certificate's ARN to use later\.

1. Create and attach an AWS IoT policy that allows devices to use the certificate to create unique device certificates and provision with the fleet provisioning template\. The following policy allows access to the device provisioning MQTT API\. For more information, see [Device provisioning MQTT API](https://docs.aws.amazon.com/iot/latest/developerguide/fleet-provision-api.html) in the *AWS IoT Core Developer Guide*\.

   Do the following:

   1. Create a file that contains the AWS IoT policy document that Greengrass core devices require\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano greengrass-provisioning-claim-iot-policy.json
      ```

      Copy the following JSON into the file\.
      + Replace each instance of *region* with the AWS Region where you set up fleet provisioning\.
      + Replace each instance of *account\-id* with your AWS account ID\.
      + Replace each instance of *GreengrassFleetProvisioningTemplate* with the name of the fleet provisioning template that you created in the previous section\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": "iot:Connect",
            "Resource": "*"
          },
          {
            "Effect": "Allow",
            "Action": [
              "iot:Publish",
              "iot:Receive"
            ],
            "Resource": [
              "arn:aws:iot:region:account-id:topic/$aws/certificates/create/*",
              "arn:aws:iot:region:account-id:topic/$aws/provisioning-templates/GreengrassFleetProvisioningTemplate/provision/*"
            ]
          },
          {
            "Effect": "Allow",
            "Action": "iot:Subscribe",
            "Resource": [
              "arn:aws:iot:region:account-id:topicfilter/$aws/certificates/create/*",
              "arn:aws:iot:region:account-id:topicfilter/$aws/provisioning-templates/GreengrassFleetProvisioningTemplate/provision/*"
            ]
          }
        ]
      }
      ```

   1. Create an AWS IoT policy from the policy document\.
      + Replace *GreengrassProvisioningClaimPolicy* with the name of the policy to create\.

      ```
      aws iot create-policy --policy-name GreengrassProvisioningClaimPolicy --policy-document file://greengrass-provisioning-claim-iot-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "policyName": "GreengrassProvisioningClaimPolicy",
        "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/GreengrassProvisioningClaimPolicy",
        "policyDocument": "{
          \"Version\": \"2012-10-17\",
          \"Statement\": [
            {
              \"Effect\": \"Allow\",
              \"Action\": \"iot:Connect\",
              \"Resource\": \"*\"
            },
            {
              \"Effect\": \"Allow\",
              \"Action\": [
                \"iot:Publish\",
                \"iot:Receive\"
              ],
              \"Resource\": [
                \"arn:aws:iot:region:account-id:topic/$aws/certificates/create/*\",
                \"arn:aws:iot:region:account-id:topic/$aws/provisioning-templates/GreengrassFleetProvisioningTemplate/provision/*\"
              ]
            },
            {
              \"Effect\": \"Allow\",
              \"Action\": \"iot:Subscribe\",
              \"Resource\": [
                \"arn:aws:iot:region:account-id:topicfilter/$aws/certificates/create/*\",
                \"arn:aws:iot:region:account-id:topicfilter/$aws/provisioning-templates/GreengrassFleetProvisioningTemplate/provision/*\"
              ]
            }
          ]
        }",
        "policyVersionId": "1"
      }
      ```

1. Attach the AWS IoT policy to the provisioning claim certificate\.
   + Replace *GreengrassProvisioningClaimPolicy* with the name of the policy to attach\.
   + Replace the target ARN with the ARN of the provisioning claim certificate\.

   ```
   aws iot attach-policy --policy-name GreengrassProvisioningClaimPolicy --target arn:aws:iot:us-west-2:123456789012:cert/aa0b7958770878eabe251d8a7ddd547f4889c524c9b574ab9fbf65f32248b1d4
   ```

   The command doesn't have any output if the request succeeds\.

You now have a provisioning claim certificate and private key that devices can use to register with AWS IoT and provision themselves as Greengrass core devices\. You can embed the claim certificate and private key in devices during manufacturing, or copy the certificate and key to devices before you install the AWS IoT Greengrass Core software\. For more information, see [Install AWS IoT Greengrass Core software with AWS IoT fleet provisioning](fleet-provisioning.md)\.
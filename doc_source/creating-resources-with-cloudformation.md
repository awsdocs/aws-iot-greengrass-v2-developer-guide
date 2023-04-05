# Creating AWS IoT Greengrass resources with AWS CloudFormation<a name="creating-resources-with-cloudformation"></a>

AWS IoT Greengrass is integrated with AWS CloudFormation, a service that helps you to model and set up your AWS resources so that you can spend less time creating and managing your resources and infrastructure\. You create a template that describes all the AWS resources that you want \(such as component versions and deployments\), and AWS CloudFormation provisions and configures those resources for you\. 

When you use AWS CloudFormation, you can reuse your template to set up your AWS IoT Greengrass resources consistently and repeatedly\. Describe your resources once, and then provision the same resources over and over in multiple AWS accounts and Regions\. 

## AWS IoT Greengrass and AWS CloudFormation templates<a name="working-with-templates"></a>

To provision and configure resources for AWS IoT Greengrass and related services, you must understand [AWS CloudFormation templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html)\. Templates are formatted text files in JSON or YAML\. These templates describe the resources that you want to provision in your AWS CloudFormation stacks\. If you're unfamiliar with JSON or YAML, you can use AWS CloudFormation Designer to help you get started with AWS CloudFormation templates\. For more information, see [What is AWS CloudFormation Designer?](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/working-with-templates-cfn-designer.html) in the *AWS CloudFormation User Guide*\.

AWS IoT Greengrass supports creating component versions and deployments  in AWS CloudFormation\. For more information, including examples of JSON and YAML templates for component versions and deployments, see the [AWS IoT Greengrass resource type reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_GreengrassV2.html) in the *AWS CloudFormation User Guide*\.

### ComponentVersion template example<a name="component-version-template"></a>

The following is the YAML template for a version of a simple component\. The JSON recipe includes line breaks for readability\.

```
Parameters:
  ComponentVersion:
    Type: String
Resources:
  TestSimpleComponentVersion:
    Type: AWS::GreengrassV2::ComponentVersion
    Properties:
      InlineRecipe: !Sub
        - "{\n
            \"RecipeFormatVersion\": \"2020-01-25\",\n
            \"ComponentName\": \"component1\",\n
            \"ComponentVersion\": \"${ComponentVersion}\",\n
            \"ComponentType\": \"aws.greengrass.generic\",\n
            \"ComponentDescription\": \"This\",\n
            \"ComponentPublisher\": \"You\",\n
            \"Manifests\": [\n
            {\n
              \"Platform\": {\n
              \"os\": \"darwin\"\n
            },\n
            \"Lifecycle\": {},\n
            \"Artifacts\": []\n
           },\n
           {\n
             \"Lifecycle\": {},\n
             \"Artifacts\": []\n
           }\n
          ],\n
          \"Lifecycle\": {\n
          \"install\": {\n
            \"script\": \"yuminstallpython\"\n
          }\n
         }\n
        }"
        - { ComponentVersion: !Ref ComponentVersion }
```

### Deployment template example<a name="deployment-template"></a>

The following is a YAML file defining a simple template for a deployment\.

```
Parameters:
  ComponentVersion:
    Type: String
  TargetArn:
    Type: String
Resources:
  TestDeployment:
    Type: AWS::GreengrassV2::Deployment
    Properties:
      Components:
        component1:
          ComponentVersion: !Ref ComponentVersion
      TargetArn: !Ref TargetArn
      DeploymentName: CloudFormationIntegrationTest
      DeploymentPolicies:
        FailureHandlingPolicy: DO_NOTHING
        ComponentUpdatePolicy:
          TimeoutInSeconds: 5000
          Action: SKIP_NOTIFY_COMPONENTS
        ConfigurationValidationPolicy:
          TimeoutInSeconds: 30000
Outputs:
  TestDeploymentArn:
    Value: !Sub
      - arn:${AWS::Partition}:greengrass:${AWS::Region}:${AWS::AccountId}:deployments:${DeploymentId}
      - DeploymentId: !GetAtt TestDeployment.DeploymentId
```

## Learn more about AWS CloudFormation<a name="learn-more-cloudformation"></a>

To learn more about AWS CloudFormation, see the following resources:
+ [AWS CloudFormation](http://aws.amazon.com/cloudformation/)
+ [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
+ [AWS CloudFormation API Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/Welcome.html)
+ [AWS CloudFormation Command Line Interface User Guide](https://docs.aws.amazon.com/cloudformation-cli/latest/userguide/what-is-cloudformation-cli.html)
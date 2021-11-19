# Customize your machine learning components<a name="ml-customization"></a>

In AWS IoT Greengrass, you can configure sample [machine learning components](perform-machine-learning-inference.md#ml-components) to customize how you perform machine learning inference on your devices with the inference, model, and runtime components as the building blocks\. AWS IoT Greengrass also provides you the flexibility to use the sample components as templates and create your own custom components as needed\. You can mix and match this modular approach to customize your machine learning inference components in the following ways:

**Using sample inference components**  
+ Modify the configuration of inference components when you deploy them\.
+ Use a custom model with the sample inference component by replacing the sample model store component with a custom model component\. Your custom model must be trained using the same runtime as the sample model\.

**Using custom inference components**  
+ Use custom inference code with the sample models and runtimes by adding public model components and runtime components as dependencies of custom inference components\.
+ Create and add custom model components or runtime components as dependencies of custom inference components\. You must use custom components if you want to use custom inference code or a runtime for which AWS IoT Greengrass doesn't provide a sample component\. 

**Topics**
+ [Modify the configuration of a public inference component](#modify-ml-component-config)
+ [Use a custom model with the sample inference component](#override-public-model-store)
+ [Create custom machine learning components](#create-private-ml-components)
+ [Create a custom inference component](#create-inference-component)

## Modify the configuration of a public inference component<a name="modify-ml-component-config"></a>

In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass), the component page displays the default configuration of that component\. For example, the default configuration of the TensorFlow Lite image classification component looks like the following:

```
{
  "accessControl": {
    "aws.greengrass.ipc.mqttproxy": {
      "aws.greengrass.TensorFlowLiteImageClassification:mqttproxy:1": {
        "policyDescription": "Allows access to publish via topic ml/tflite/image-classification.",
        "operations": [
          "aws.greengrass#PublishToIoTCore"
        ],
        "resources": [
          "ml/tflite/image-classification"
        ]
      }
    }
  },
  "PublishResultsOnTopic": "ml/tflite/image-classification",
  "ImageName": "cat.jpeg",
  "InferenceInterval": 3600,
  "ModelResourceKey": {
    "model": "TensorFlowLite-Mobilenet"
  }
}
```

When you deploy a public inference component, you can modify the default configuration to customize your deployment\. For information about the available configuration parameters for each public inference component, see the component topic in [AWS\-provided machine learning components](perform-machine-learning-inference.md#ml-components)\.

This section describes how to deploy a modified component from the AWS IoT Greengrass console\. For information about deploying components using the AWS CLI, see [Create deployments](create-deployments.md)\.<a name="modify-ml-component-config-console"></a>

**To deploy a modified public inference component \(console\)**

1. Sign in to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. In the navigation menu, choose **Components**\.

1. On the **Components** page, on the **Public components** tab, choose the component you want to deploy\.

1. On the component page, choose **Deploy**\.

1. <a name="add-deployment"></a>From **Add to deployment**, choose one of the following:

   1. To merge this component to an existing deployment on your target device, choose **Add to existing deployment**, and then select the deployment that you want to revise\.

   1. To create a new deployment on your target device, choose **Create new deployment**\. If you have an existing deployment on your device, choosing this step replaces the existing deployment\. 

1. <a name="specify-deployment-target"></a>On the **Specify target** page, do the following: 

   1. Under **Deployment** information, enter or modify the friendly name for your deployment\.

   1. Under **Deployment targets**, select a target for your deployment, and choose **Next**\. You cannot change the deployment target if you are revising an existing deployment\.

1. On the **Select components** page, under **Public components** verify that the inference component with your modified configuration is selected, and choose **Next**\.

1. On the **Configure components** page, do the following: 

   1. Select the inference component, and choose **Configure component**\.

   1. Under **Configuration update**, enter the configuration values that you want to update\. For example, enter the following configuration update in the **Configuration to merge** box to change the inference interval to 15 seconds, and instruct the component to look for the image named `custom.jpg` in the `/custom-ml-inference/images/` folder\. 

      ```
      {
        "InferenceInterval": "15",
        "ImageName": "custom.jpg",
        "ImageDirectory": "/custom-ml-inference/images/"
      }
      ```

      To reset a component's entire configuration to its default values, specify a single empty string `""` in the **Reset paths** box\. 

   1. Choose **Confirm**, and then choose **Next**\.

1. On the **Configure advanced setting** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, choose **Deploy**

## Use a custom model with the sample inference component<a name="override-public-model-store"></a>

If you want to use the sample inference component with your own machine learning models for a runtime for which AWS IoT Greengrass provides a sample runtime component, you must override the public model components with components that use those models as artifacts\. At a high\-level you complete the following steps to use a custom model with the sample inference component:

1. Create a model component that uses a custom model in an S3 bucket as an artifact\. Your custom model must be trained using the same runtime as the model that you want to replace\.

1. Modify the `ModelResourceKey` configuration parameter in the inference component to use the custom model\. For information about updating the configuration of the inference component, see [Modify the configuration of a public inference component](#modify-ml-component-config)

When you deploy the inference component, AWS IoT Greengrass looks for the latest version of its component dependencies\. It overrides the dependent public model component if a later custom version of the component exists in the same AWS account and AWS Region\. 

### Create a custom model component \(console\)<a name="create-model-store-component-console"></a>

1. Upload your model to an S3 bucket\. For information about uploading your models to an S3 bucket, see [Working with Amazon S3 Buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html) in the *Amazon Simple Storage Service User Guide*\.
**Note**  <a name="s3-artifacts-note"></a>
<a name="sr-artifacts-req"></a>You must store your artifacts in S3 buckets that are in the same AWS account and AWS Region as the components\. To enable AWS IoT Greengrass to access these artifacts, the [Greengrass device role](device-service-role.md) must allow the `s3:GetObject` action\. For more information about the device role, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

1. Retrieve the component recipe for the public model store component\.

   1. On the **Components** page, on the **Public components** tab, look for and choose the public model component for which you want to create a new version\. For example, `variant.DLR.ImageClassification.ModelStore`\.

   1. On the component page, choose **View recipe** and copy the displayed JSON recipe\.

1. On the **Components** page, on the **My components** tab, choose **Create component**\.

1. On the **Create component** page, under **Component information**, select **Enter recipe as JSON** as your component source\.

1. In the **Recipe** box, paste the component recipe that you previously copied\.

1. <a name="override-model-recipe-config"></a>In the recipe, update the following values:
   + `ComponentVersion`: Increment the minor version of the component\. 

     When you create a custom component to override a public model component, you must update only the minor version of the existing component version\. For example, if the public component version is `2.1.0`, you can create a custom component with version `2.1.1`\.
   + `Manifests.Artifacts.Uri`: Update each URI value to the Amazon S3 URI of the model that you want to use\.
**Note**  
Do not change the name of the component\.

1. Choose **Create component**\.

### Create a custom model component \(AWS CLI\)<a name="create-model-store-component-cli"></a>

1. Upload your model to an S3 bucket\. For information about uploading your models to an S3 bucket, see [Working with Amazon S3 Buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html) in the *Amazon Simple Storage Service User Guide*\.
**Note**  <a name="s3-artifacts-note"></a>
<a name="sr-artifacts-req"></a>You must store your artifacts in S3 buckets that are in the same AWS account and AWS Region as the components\. To enable AWS IoT Greengrass to access these artifacts, the [Greengrass device role](device-service-role.md) must allow the `s3:GetObject` action\. For more information about the device role, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

1. Run the following command to retrieve the component recipe of the public component\. This command writes the component recipe to the output file that you provide in your command\. Convert the retrieved base64\-encoded string to JSON or YAML, as needed\.

------
#### [ Linux, macOS, or Unix ]

   ```
   aws greengrassv2 get-component \
       --arn <arn> \
       --recipe-output-format <recipe-format> \
       --query recipe \
       --output text | base64 --decode > <recipe-file>
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws greengrassv2 get-component ^
       --arn <arn> ^
       --recipe-output-format <recipe-format> ^
       --query recipe ^
       --output text > <recipe-file>.base64
   
   certutil -decode <recipe-file>.base64 <recipe-file>
   ```

------
#### [ PowerShell ]

   ```
   aws greengrassv2 get-component `
       --arn <arn> `
       --recipe-output-format <recipe-format> `
       --query recipe `
       --output text > <recipe-file>.base64
   
   certutil -decode <recipe-file>.base64 <recipe-file>
   ```

------

1. Update the name of the recipe file to `<component-name>-<component-version>`, where component version is the target version of the new component\. For example, `variant.DLR.ImageClassification.ModelStore-2.1.1.yaml`\. 

1. <a name="override-model-recipe-config"></a>In the recipe, update the following values:
   + `ComponentVersion`: Increment the minor version of the component\. 

     When you create a custom component to override a public model component, you must update only the minor version of the existing component version\. For example, if the public component version is `2.1.0`, you can create a custom component with version `2.1.1`\.
   + `Manifests.Artifacts.Uri`: Update each URI value to the Amazon S3 URI of the model that you want to use\.
**Note**  
Do not change the name of the component\.

1. Run the following command to create a new component using the recipe you retrieved and modified\.

   ```
   aws greengrassv2 create-component-version \
       --inline-recipe fileb://path/to/component/recipe
   ```
**Note**  
This step creates the component in the AWS IoT Greengrass service in the AWS Cloud\. You can use the Greengrass CLI to develop, test, and deploy your component locally before you upload it to the cloud\. For more information, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md)\.

For more information about creating components, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md)\.

## Create custom machine learning components<a name="create-private-ml-components"></a>

You must create custom components if you want to use custom inference code or a runtime for which AWS IoT Greengrass doesn't provide a sample component\. You can use your custom inference code with the AWS\-provided sample machine learning models and runtimes, or you can develop a completely customized machine learning inference solution with your own models and runtime\. If your models use a runtime for which AWS IoT Greengrass provides a sample runtime component, then you can use that runtime component, and you need to create custom components only for your inference code and the models you want to use\. 

**Topics**
+ [Retrieve the recipe for a public component](#get-ml-component-recipes)
+ [Retrieve sample component artifacts](#get-ml-component-artifacts)
+ [Upload component artifacts to an S3 bucket](#upload-ml-component-artifacts)
+ [Create custom components](#create-ml-components)

### Retrieve the recipe for a public component<a name="get-ml-component-recipes"></a>

You can use the recipe of an existing public machine learning component as a template to create a custom component\. To view the component recipe for the latest version of a public component, use the console or the AWS CLI as follows:
+ **Using the console**

  1. On the **Components** page, on the **Public components** tab, look for and choose the public component\.

  1. On the component page, choose **View recipe**\.
+ **Using AWS CLI**

  Run the following command to retrieve the component recipe of the public variant component\. This command writes the component recipe to the JSON or YAML recipe file that you provide in your command\. 

------
#### [ Linux, macOS, or Unix ]

  ```
  aws greengrassv2 get-component \
      --arn <arn> \
      --recipe-output-format <recipe-format> \
      --query recipe \
      --output text | base64 --decode > <recipe-file>
  ```

------
#### [ Windows Command Prompt \(CMD\) ]

  ```
  aws greengrassv2 get-component ^
      --arn <arn> ^
      --recipe-output-format <recipe-format> ^
      --query recipe ^
      --output text > <recipe-file>.base64
  
  certutil -decode <recipe-file>.base64 <recipe-file>
  ```

------
#### [ PowerShell ]

  ```
  aws greengrassv2 get-component `
      --arn <arn> `
      --recipe-output-format <recipe-format> `
      --query recipe `
      --output text > <recipe-file>.base64
  
  certutil -decode <recipe-file>.base64 <recipe-file>
  ```

------

  Replace the values in your command as follows:
  + `<arn>`\. The Amazon Resource Name \(ARN\) of the public component\. 
  + `<recipe-format>`\. The format in which you want to create the recipe file\. Supported values are `JSON` and `YAML`\.
  + `<recipe-file>`\. The name of the recipe in the format `<component-name>-<component-version>`\. 

### Retrieve sample component artifacts<a name="get-ml-component-artifacts"></a>

You can use the artifacts used by the public machine learning components as templates to create your custom component artifacts, such as inference code or runtime installation scripts\. 

To view the sample artifacts that are included in the public machine learning components, deploy the public inference component and then view the artifacts on your device in the `/greengrass/v2/packages/artifacts-unarchived/component-name/component-version/` folder\. 

### Upload component artifacts to an S3 bucket<a name="upload-ml-component-artifacts"></a>

Before you can create a custom component, you must upload the component artifacts to an S3 bucket and use the S3 URIs in your component recipe\. For example, to use custom inference code in your inference component, upload the code to an S3 bucket\. You can then use the Amazon S3 URI of your inference code as an artifact in your component\. 

For information about uploading content to an S3 bucket, see [Working with Amazon S3 Buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html) in the *Amazon Simple Storage Service User Guide*\.

**Note**  <a name="s3-artifacts-note"></a>
<a name="sr-artifacts-req"></a>You must store your artifacts in S3 buckets that are in the same AWS account and AWS Region as the components\. To enable AWS IoT Greengrass to access these artifacts, the [Greengrass device role](device-service-role.md) must allow the `s3:GetObject` action\. For more information about the device role, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

### Create custom components<a name="create-ml-components"></a>

You can use the artifacts and recipes that you retrieved to create your custom machine learning components\. For an example, see [Create a custom inference component](#create-inference-component)\.

For detailed information about creating and deploying components to Greengrass devices, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md) and [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

## Create a custom inference component<a name="create-inference-component"></a>

This section shows you how to create a custom inference component using the DLR image classification component as a template\.

**Topics**
+ [Upload your inference code to an Amazon S3 bucket](#create-inference-code)
+ [Create a recipe for your inference component](#create-inference-component-recipe)
+ [Create the inference component](#create-private-inference-component)

### Upload your inference code to an Amazon S3 bucket<a name="create-inference-code"></a>

Create your inference code and then upload it to an S3 bucket\. For information about uploading content to an S3 bucket, see [Working with Amazon S3 Buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html) in the *Amazon Simple Storage Service User Guide*\.

**Note**  <a name="s3-artifacts-note"></a>
<a name="sr-artifacts-req"></a>You must store your artifacts in S3 buckets that are in the same AWS account and AWS Region as the components\. To enable AWS IoT Greengrass to access these artifacts, the [Greengrass device role](device-service-role.md) must allow the `s3:GetObject` action\. For more information about the device role, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

### Create a recipe for your inference component<a name="create-inference-component-recipe"></a>

1. Run the following command to retrieve the component recipe of the DLR image classification component\. This command writes the component recipe to the JSON or YAML recipe file that you provide in your command\. 

------
#### [ Linux, macOS, or Unix ]

   ```
   aws greengrassv2 get-component \
       --arn arn:aws:greengrass:region:aws:components:aws.greengrass.DLRImageClassification:versions:version \
       --recipe-output-format JSON | YAML \
       --query recipe \
       --output text | base64 --decode > <recipe-file>
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws greengrassv2 get-component ^
       --arn arn:aws:greengrass:region:aws:components:aws.greengrass.DLRImageClassification:versions:version ^
       --recipe-output-format JSON | YAML ^
       --query recipe ^
       --output text > <recipe-file>.base64
   
   certutil -decode <recipe-file>.base64 <recipe-file>
   ```

------
#### [ PowerShell ]

   ```
   aws greengrassv2 get-component `
       --arn arn:aws:greengrass:region:aws:components:aws.greengrass.DLRImageClassification:versions:version `
       --recipe-output-format JSON | YAML `
       --query recipe `
       --output text > <recipe-file>.base64
   
   certutil -decode <recipe-file>.base64 <recipe-file>
   ```

------

   Replace *<recipe\-file>* with the name of the recipe in the format `<component-name>-<component-version>`\. 

1. In the `ComponentDependencies` object in your recipe, do one or more of the following depending on the model and runtime components that you want to use:
   + Keep the DLR component dependency if you want to use DLR\-compiled models\. You can also replace it with a dependency on a custom runtime component, as shown in the following example\.

     **Runtime component**

------
#### [ JSON ]

     ```
     { 
         "<runtime-component>": {
             "VersionRequirement": "<version>",
             "DependencyType": "HARD"
         }
     }
     ```

------
#### [ YAML ]

     ```
     <runtime-component>:
         VersionRequirement: "<version>"
         DependencyType: HARD
     ```

------
   + Keep the DLR image classification model store dependency to use the pre\-trained ResNet\-50 models that AWS provides, or modify it to use a custom model component\. When you include a dependency for a public model component, if a later custom version of the component exists in the same AWS account and AWS Region, then the inference component uses that custom component\. Specify the model component dependency as shown in the following examples\.

     **Public model component**

------
#### [ JSON ]

     ```
     {
         "variant.DLR.ImageClassification.ModelStore": {
             "VersionRequirement": "<version>",
             "DependencyType": "HARD"
         }
     }
     ```

------
#### [ YAML ]

     ```
     variant.DLR.ImageClassification.ModelStore:
         VersionRequirement: "<version>"
         DependencyType: HARD
     ```

------

     **Custom model component**

------
#### [ JSON ]

     ```
     {
         "<custom-model-component>": {
             "VersionRequirement": "<version>",
             "DependencyType": "HARD"
         }
     }
     ```

------
#### [ YAML ]

     ```
     <custom-model-component>:
         VersionRequirement: "<version>"
         DependencyType: HARD
     ```

------

1. In the `ComponentConfiguration` object, add the default configuration for this component\. You can later modify this configuration when you deploy the component\. The following excerpt shows the component configuration for the DLR image classification component\. 

   For example, if you use a custom model component as a dependency for your custom inference component, then modify `ModelResourceKey` to provide the names of the models that you are using\.

------
#### [ JSON ]

   ```
   {
     "accessControl": {
       "aws.greengrass.ipc.mqttproxy": {
         "aws.greengrass.ImageClassification:mqttproxy:1": {
           "policyDescription": "Allows access to publish via topic ml/dlr/image-classification.",
           "operations": [
             "aws.greengrass#PublishToIoTCore"
           ],
           "resources": [
             "ml/dlr/image-classification"
           ]
         }
       }
     },
     "PublishResultsOnTopic": "ml/dlr/image-classification",
     "ImageName": "cat.jpeg",
     "InferenceInterval": 3600,
     "ModelResourceKey": {
       "armv7l": "DLR-resnet50-armv7l-cpu-ImageClassification"
       "x86_64": "DLR-resnet50-x86_64-cpu-ImageClassification"
       "aarch64": "DLR-resnet50-aarch64-cpu-ImageClassification"
     }
   }
   ```

------
#### [ YAML ]

   ```
   accessControl:
       aws.greengrass.ipc.mqttproxy:
           'aws.greengrass.ImageClassification:mqttproxy:1':
               policyDescription: 'Allows access to publish via topic ml/dlr/image-classification.'
               operations:
                   - 'aws.greengrass#PublishToIoTCore'
               resources:
                   - ml/dlr/image-classification
   PublishResultsOnTopic: ml/dlr/image-classification
   ImageName: cat.jpeg
   InferenceInterval: 3600
   ModelResourceKey:
       armv7l: "DLR-resnet50-armv7l-cpu-ImageClassification"
       x86_64: "DLR-resnet50-x86_64-cpu-ImageClassification"
       aarch64: "DLR-resnet50-aarch64-cpu-ImageClassification"
   ```

------

1. In the `Manifests` object, provide information about the artifacts and the configuration of this component that are used when the component is deployed to different platforms and any other information required to successfully run the component\. The following excerpt shows the configuration of the `Manifests` object for Linux platform in the DLR image classification component\.

------
#### [ JSON ]

   ```
   {
     "Manifests": [
       {
         "Platform": {
           "os": "linux",
           "architecture": "arm"
         },
         "Name": "32-bit armv7l - Linux (raspberry pi)",
         "Artifacts": [
           {
             "URI": "s3://SAMPLE-BUCKET/sample-artifacts-directory/image_classification.zip",
             "Unarchive": "ZIP"
           }
         ],
         "Lifecycle": {
           "Setenv": {
             "DLR_IC_MODEL_DIR": "{variant.DLR.ImageClassification.ModelStore:artifacts:decompressedPath}/{configuration:/ModelResourceKey/armv7l}",
             "DEFAULT_DLR_IC_IMAGE_DIR": "{artifacts:decompressedPath}/image_classification/sample_images/"
           },
           "Run": {
             "RequiresPrivilege": true,
             "script": ". {variant.DLR:configuration:/MLRootPath}/greengrass_ml_dlr_venv/bin/activate\npython3 {artifacts:decompressedPath}/image_classification/inference.py"
           }
         }
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   Manifests:
     - Platform:
         os: linux
         architecture: arm
       Name: 32-bit armv7l - Linux (raspberry pi)
       Artifacts:
         - URI: s3://SAMPLE-BUCKET/sample-artifacts-directory/image_classification.zip
           Unarchive: ZIP
       Lifecycle:
         Setenv:
           DLR_IC_MODEL_DIR: "{variant.DLR.ImageClassification.ModelStore:artifacts:decompressedPath}/{configuration:/ModelResourceKey/armv7l}"
           DEFAULT_DLR_IC_IMAGE_DIR: "{artifacts:decompressedPath}/image_classification/sample_images/"
         Run:
           RequiresPrivilege: true
           script: |-
             . {variant.DLR:configuration:/MLRootPath}/greengrass_ml_dlr_venv/bin/activate
             python3 {artifacts:decompressedPath}/image_classification/inference.py
   ```

------

 For detailed information about creating component recipes, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.

### Create the inference component<a name="create-private-inference-component"></a>

Use the AWS IoT Greengrass console or the AWS CLI to create a component using the recipe you just defined\. After you create the component, you can deploy it to perform inference on your device\. For an example of how to deploy an inference component, see [Tutorial: Perform sample image classification inference using TensorFlow Lite](ml-tutorial-image-classification.md)\.

#### Create custom inference component \(console\)<a name="create-inference-component-console"></a>

1. Sign in to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. In the navigation menu, choose **Components**\.

1. On the **Components** page, on the **My components** tab, choose **Create component**\.

1. On the **Create component** page, under **Component information**, select either **Enter recipe as JSON** or **Enter recipe as YAML** as your component source\.

1. In the **Recipe** box, enter the custom recipe that you created\. 

1. Click **Create component**\.

#### Create custom inference component \(AWS CLI\)<a name="create-inference-component-cli"></a>

Run the following command to create a new custom component using the recipe that you created\.

```
aws greengrassv2 create-component-version \
    --inline-recipe fileb://path/to/recipe/file
```

**Note**  
This step creates the component in the AWS IoT Greengrass service in the AWS Cloud\. You can use the Greengrass CLI to develop, test, and deploy your component locally before you upload it to the cloud\. For more information, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md)\.
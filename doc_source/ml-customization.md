# Customize your machine learning components<a name="ml-customization"></a>

In AWS IoT Greengrass, you can configure the [public machine learning components](ml-components.md) to customize how you perform machine learning inference on your devices with the inference, runtime and model components as the building blocks\. AWS IoT Greengrass also provides you the flexibility to use the public components as templates and create your own custom inference, model, or runtime components as needed\. You can mix and match this modular approach to customize your machine learning inference components in the following ways:

**Using public inference components**  
+ Modify the configuration of inference components when you deploy them\.
+ Create customized private model components that use the same name as the public `variant.*` model components\.

**Using private inference components**  
+ Add public model components, runtime components, or both as dependencies\.
+ Create and add customized private model components, runtime components, or both as dependencies\. 

## Modify the configuration of a public inference component<a name="modify-ml-component-config"></a>

In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass), the component page displays the default configuration of that component\. For example, the default configuration of the DLR Image Classification component looks like the following:

```
{
  "ImageName": "cat.jpeg",
  "InferenceInterval": "3600",
  "MLRootPath": "$HOME/greengrass_ml",
  "Accelerator": "cpu",
  "ModelResourceKey": {
    "armv7l": "DLR-resnet50-armv7l-cpu-ImageClassification",
    "x86_64": "DLR-resnet50-x86_64-cpu-ImageClassification"
  }
}
```

When you deploy a public inference component, you can modify the default configuration to customize your deployment\. For information about the configuration parameters that you can update for each public inference component, see [Public machine learning components](ml-components.md)\.

This section describes how to deploy a modified component from the AWS IoT Greengrass console\. For information about deploying components using the AWS CLI, see [Create deployments](create-deployments.md)\.<a name="modify-ml-component-config-console"></a>

**To deploy a modified public inference component \(console\)**

1. Sign in to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. In the navigation menu, choose **Components**\.

1. On the **Components** page, on the **Public components** tab, choose the component you want to deploy\.

1. On the component page, choose **Deploy**\.

1. <a name="add-deployment"></a>From **Add to deployment**, choose one of the following:

   1. To merge this component to an existing deployment on your target device, choose **Add to existing deployment** and then select the deployment that you want to revise\.

   1. To create a new deployment on your target device, then choose **Create new deployment**\. If you have an existing deployment on your device, then choosing this step will replace the existing deployment\. 

1. <a name="specify-deployment-target"></a>On the **Specify target** page, do the following: 

   1. Under **Deployment** information, enter or modify the friendly name for your deployment\.

   1. Under **Deployment targets**, select a target for your deployment, and choose **Next**\. You cannot change the deployment target if you are revising an existing deployment\.

1. On the **Select components** page, under **Public components** verify that the inference component with your modified configuration is selected, and choose **Next**\.

1. On the **Configure components** page, do the following: 

   1. Select the inference component, and choose **Configure component**\.

   1. Under **Configuration update**, enter the configuration values that you want to update\. For example, to update the configuration for the DLR Image Classification object to change the inference interval to 15 seconds, enter the following content in the **Configuration to merge** box\.

      ```
      {
        "InferenceInterval": "15"
      }
      ```

      To reset a component's entire configuration to its default values, specify a single empty string `""` in the **Reset paths** box\. 

   1. Choose **Confirm**, and then choose **Next**\.

1. On the **Configure advanced setting** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, choose **Deploy**

## Create a private version of a public variant model component<a name="create-variant-component"></a>

If you want to bring your own machine learning models compiled using Amazon SageMaker Neo Deep Learning Runtime and plug them into an inference component provided by AWS, you must first upload those artifacts to an S3 bucket\. You can then simply use the Amazon S3 URIs of your model as artifacts in private versions of the corresponding public variant model components\. Make sure you don't change the name of the variant component in the new private version\. When you deploy an inference component, AWS IoT Greengrass looks for the latest version of its component dependencies and uses available private components instead of the public components\. 

For information about uploading your models to an S3 bucket, see [Working with Amazon S3 Buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html) in the *Amazon Simple Storage Service Developer Guide*\.

### To create a private variant component \(console\)<a name="create-variant-component-console"></a>

1. Sign in to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. In the navigation menu, choose **Components**\.

1. Retrieve the component recipe for the public model component\.

   1. On the **Components** page, on the **Public components** tab, look for and choose the public model component for which you want to create a new version\. For example, `variant.ImageClassification.ModelStore`\.

   1. On the component page, choose **View recipe** and copy the displayed JSON recipe\.

1. On the **Components** page, on the **My components** tab, choose **Create component**\.

1. On the **Create component** page, under **Component information**, select **Enter recipe as JSON** as your component source\.

1. In the **Recipe** box, paste the component recipe that you previously copied\.

1. In the recipe, update the following values:
   + `ComponentVersion`: Increment the minor version of the component\. 

     When you create a private variant component for use with the public inference component, make sure you update only the minor version of the existing component version\.\. That is, use a version number that is between `2.0.0` and `2.1.0`\. For example, if the public component version is `2.0.0`, you can create a private variant component with version `2.0.1`\.
   + `Manifests.Artifacts.Uri`: Update each URI value to the Amazon S3 URI of the model or installer script that you want to use\.
**Note**  
Do not change the name of the component\.

1. Choose **Create component**\.

### To create a private variant component \(AWS CLI\)<a name="create-variant-component-cli"></a>

1. Run the following command to retrieve the component recipe of the public component\. This command writes the component recipe to the output file that you provide in your command\. Convert the retrieved base64\-encoded string to JSON or YAML, as needed\.

   ```
   aws greengrassv2 get-component \
       --arn <arn> \
       --recipe-output-format <recipe-format> \
       --query recipe \
       --output text | base64 --decode > <recipe-file>
   ```

1. Update the name of the recipe file to `<component-name>-<component-version>`, where component version is the target version of the new component\. For example, `variant.ImageClassification.ModelStore-2.0.1.yaml`\. 

1. In the recipe, update the following values:
   + `ComponentVersion`: Increment the minor version of the component\. Use the same version in the recipe and in the recipe file name\.

     When you create a private variant component for use with the public inference component, make sure you update only the minor version of the existing component version\.\. That is, use a version number that is between `2.0.0` and `2.1.0`\. For example, if the public component version is `2.0.0`, you can create a private variant component with version `2.0.1`\.
   + `Manifests.Artifacts.Uri`\. Update each URI value to the Amazon S3URI of the model or installer script that you want to use\.

1. Run the following command to create a new private component using the recipe you retrieved and modified\.

   ```
   aws greengrassv2 create-component-version \
       --inline-recipe fileb://path/to/component/recipe
   ```

For more information about creating components, see [Create custom AWS IoT Greengrass components](create-components.md)\.

## Create a private inference component<a name="create-inference-component"></a>

You must create private inference components if you want to use custom inference code\. You can use either the public model and runtime components provided by AWS as dependencies in your private inference component, or you can develop a completely customized machine learning inference solution with your own models and runtime\. If you use DLR as your runtime, then you can use the public DLR Installer component, and you need to provide only custom inference code and any custom models you want to use\. 

To view the artifacts used by the public components, you can deploy the public inference components and then view the artifacts, such as the DLR installer script and the inference code, on your device in the `/greengrass/v2/packages/artifacts-unarchived/component-name/component-version/` folder\. You can then modify these artifacts as needed and use them to create your own custom components\. 

**Topics**
+ [Create a recipe for your inference component](#create-inference-component-recipe)
+ [Create the inference component](#create-private-inference-component)

 For detailed information about creating component recipes, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.

### Create a recipe for your inference component<a name="create-inference-component-recipe"></a>

1. Start from the recipe for a [public inference component](ml-components.md) or from one of the [recipe examples](component-recipe-reference.md#recipe-examples) in this guide\.

1. In the `ComponentDependencies` object in your recipe, do one or more of the following depending on the model and runtime components that you want to use:
   + Include the following dependency for a runtime component\. When you include a dependency for the `variant.DLR` component, if a private version of the component exists in your component registry, then the inference component will use that private component\.

     **Public runtime component**

------
#### [ JSON ]

     ```
     { 
         "variant.DLR": {
             "VersionRequirement": "<version>",
             "DependencyType": "HARD"
         }
     }
     ```

------
#### [ YAML ]

     ```
     variant.DLR:
         VersionRequirement: "<version>"
         DependencyType: HARD
     ```

------

     **Private runtime component**

------
#### [ JSON ]

     ```
     { 
         "<custom-runtime-component>": {
             "VersionRequirement": "<version>",
             "DependencyType": "HARD"
         }
     }
     ```

------
#### [ YAML ]

     ```
     <custom-runtime-component>:
         VersionRequirement: "<version>"
         DependencyType: HARD
     ```

------
   + Include the following dependency for a model component\. When you include a dependency for a variant model component, if a private version of the component exists in your component registry, then the inference component will use that private component\.

     **Public model component**

------
#### [ JSON ]

     ```
     {
         variant.<type>.ModelStore: {
             "VersionRequirement": "<version>",
             "DependencyType": "HARD"
         }
     }
     ```

------
#### [ YAML ]

     ```
     variant.<type>.ModelStore:
         VersionRequirement: "<version>"
         DependencyType: HARD
     ```

------

     **Private model component**

------
#### [ JSON ]

     ```
     {
         <custom-model-component>: {
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

1. In the `ComponentConfiguration` object, add the default configuration for this component\. You can later modify this configuration when you deploy the component\. The following excerpt shows the component configuration for the DLR Image Classification component that you can use as a template for your custom inference component\. 

   For example, if you use a private model component as a dependency for your custom inference component, then modify `ModelResourceKey` to provide the names of the models that you are using\.

------
#### [ JSON ]

   ```
   { 
       "ComponentConfiguration": {
           "DefaultConfiguration": {
               "Accelerator": "cpu",
               "MLRootPath": "$HOME/greengrass_ml",
               "ImageName": "cat.jpeg",
               "InferenceInterval": 3600,
               "ModelResourceKey": {
                 "armv7l": "DLR-resnet50-armv7l-cpu-ImageClassification",
                 "x86_64": "DLR-resnet50-x86_64-cpu-ImageClassification"
               }
           }
       }
   }
   ```

------
#### [ YAML ]

   ```
   ComponentConfiguration:
     DefaultConfiguration:
       Accelerator: "cpu"
       MLRootPath: "$HOME/greengrass_ml"
       ImageName: "cat.jpeg"
       InferenceInterval: 3600
       ModelResourceKey:
         armv7l: "DLR-resnet50-armv7l-cpu-ImageClassification"
         x86_64: "DLR-resnet50-x86_64-cpu-ImageClassification"
   ```

------

1. In the `Manifests` object, provide information about the artifacts and the configuration of this component that are used when the component is deployed to different platforms and any other information required to successfully run the component\. The following excerpt shows the `Manifests` object for the DLR Image Classification component that you can use as a template for your custom inference component\.

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
                  "URI": "s3://$bucketName$/$testArtifactsDirectory$/image_classification.zip",
                  "Unarchive": "ZIP"
               },
               {
                  "URI": "s3://$bucketName$/$testArtifactsDirectory$/init.sh"
               }
            ],
            "Lifecycle": {
               "Install": {
                  "RequiresPrivilege": true,
                  "script": "bash {artifacts:path}/init.sh {artifacts:decompressedPath}/image_classification/sample_images {configuration:/MLRootPath}\nbash {variant.DLR:artifacts:path}/installer.sh -a {configuration:/Accelerator} -p {configuration:/MLRootPath}",
                  "timeout": 900
               },
               "Run": {
                  "RequiresPrivilege": true,
                  "script": ". {configuration:/MLRootPath}/greengrass_ml_dlr_venv/bin/activate\npython3 {artifacts:decompressedPath}/image_classification/inference.py -a {configuration:/Accelerator} -m {variant.ImageClassification.ModelStore:artifacts:decompressedPath}/{configuration:/ModelResourceKey/armv7l} -p {configuration:/MLRootPath} -i {configuration:/ImageName} -s {configuration:/InferenceInterval}"
               },
               "shutdown": {
                  "RequiresPrivilege": true,
                  "script": "deactivate"
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
         - URI: s3://$bucketName$/$testArtifactsDirectory$/image_classification.zip
           Unarchive: ZIP
         - URI: s3://$bucketName$/$testArtifactsDirectory$/init.sh
       Lifecycle:
         Install:
           RequiresPrivilege: true
           script: |-
             bash {artifacts:path}/init.sh {artifacts:decompressedPath}/image_classification/sample_images {configuration:/MLRootPath}
             bash {variant.DLR:artifacts:path}/installer.sh -a {configuration:/Accelerator} -p {configuration:/MLRootPath}
           timeout: 900
         Run:
           RequiresPrivilege: true
           script: |-
             . {configuration:/MLRootPath}/greengrass_ml_dlr_venv/bin/activate
             python3 {artifacts:decompressedPath}/image_classification/inference.py -a {configuration:/Accelerator} -m {variant.ImageClassification.ModelStore:artifacts:decompressedPath}/{configuration:/ModelResourceKey/armv7l} -p {configuration:/MLRootPath} -i {configuration:/ImageName} -s {configuration:/InferenceInterval}
         shutdown:
           RequiresPrivilege: true
           script: |-
             deactivate
   ```

------

### Create the inference component<a name="create-private-inference-component"></a>

Use the AWS IoT Greengrass console or the AWS CLI to create a component using the recipe you just defined\. After you create the component, you can deploy it to perform inference on your device\. For an example of how to deploy an inference component, see [Deploy the DLR Image Classification component](ml-tutorial-image-classification.md#ml-image-classification-deploy)\.

#### To create your inference component \(console\)<a name="create-inference-component-console"></a>

1. Sign in to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. In the navigation menu, choose **Components**\.

1. On the **Components** page, on the **My components** tab, choose **Create component**\.

1. On the **Create component** page, under **Component information**, select either **Enter recipe as JSON** or **Enter recipe as YAML** as your component source\.

1. In the **Recipe** box, enter the custom recipe that you created\. 

1. Click **Create component**\.

#### To create your inference component \(AWS CLI\)<a name="create-inference-component-cli"></a>

Run the following command to create a new private component using the recipe that you created\.

```
aws greengrassv2 create-component-version \
    --inline-recipe fileb://path/to/recipe/file
```
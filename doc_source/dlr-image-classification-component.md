# DLR Image Classification<a name="dlr-image-classification-component"></a>

 You can use the DLR Image Classification component \(`aws.greengrass.DLRImageClassification`\) to perform a sample image classification inference on supported devices\. This component installs DLR and its dependencies and downloads sample ML models to perform this inference\. 

This sample inference component uses the variant [Image Classification Model Store](variant-image-classification-component.md) and the [DLR Installer](variant-dlr-component.md) components as dependencies to [perform inference](ml-tutorial-image-classification.md)\. To use a different model or runtime to perform image classification, you can [create private versions](ml-customization.md#create-variant-component) of the dependent variant components\. If you want create your own [private inference component](ml-customization.md#create-inference-component), you can use the recipe of this component as a template\. 

This component has the following versions:
+ 2\.0\.x

## Requirements<a name="dlr-image-classification-component-requirements"></a><a name="dlr-supported-platforms"></a>

This component requires one of the following devices configured for use with AWS IoT Greengrass\. For more information see [Setting up AWS IoT Greengrass Version 2](setting-up.md)\.
+ **32\-bit Armv7l**

  [Raspberry Pi](https://www.raspberrypi.org) running Raspberry Pi OS, 2020\-08\-24\.
+ **64\-bit x86\_64**

  Device running Amazon Linux 2 or Ubuntu 18\.04, with [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later\.

## Configuration<a name="ml-image-classification-config"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.<a name="ml-inference-config-params"></a>

`Accelerator`  
Do not modify\. Currently, the only supported value for the accelerator is `cpu`, because the models in the dependent model components are compiled only for the CPU accelerator\.

`MLRootPath`  
\(Optional\) The path of the folder on the device where inference components read images and write inference results\. You can modify this value to any location on your device to which you have read/write access\.  
Default: `$HOME/greengrass_ml`

`ImageName`  
\(Optional\) The image that the inference component uses as an input to a make prediction\. You can modify this value to any image that is located in `MLRootPath/images`\. AWS IoT Greengrass supports the following formats: `jpeg`, `jpg`, `png`, and `npy`\.   
Default: Static image

`InferenceInterval`  
\(Optional\) The time in seconds between each prediction made by the inference code\. The sample inference code runs indefinitely and repeats its predictions at the specified time interval\. For example, you can change this to a shorter interval if you want to mount images taken by a camera to `MLRootPath/images` and use those images for real\-time prediction\.  
Default: `3600`

`ModelResourceKey`  
Do not modify\. This object specifies the models that are used in the dependent public model component\. 

## Component recipe<a name="dlr-image-classification-component-recipe"></a><a name="view-component-recipe"></a>

To view the component recipe for the latest version of a public component, do one of the following:
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
#### [ Windows command prompt ]

  ```
  aws greengrassv2 get-component ^
      --arn <arn> ^
      --recipe-output-format <recipe-format> ^
      --query recipe ^
      --output text > <recipe-file>.base64
  
  certutil -decode <recipe-file>.base64 <recipe-file>
  ```

------

  Replace the values in your command as follows:
  + `<arn>`\. The Amazon Resource Name \(ARN\) of the public component\. 
  + `<recipe-format>`\. The format in which you want to create the recipe file\. Supported values are `JSON` and `YAML`\.
  + `<recipe-file>`\. The name of the recipe in the format `<component-name>-<component-version>`\. 

The following excerpt shows the component recipe for version 2\.0\.0 of the component\. 

------
#### [ YAML ]

```
RecipeFormatVersion: "2020-01-25"
ComponentName: aws.greengrass.DLRImageClassification
ComponentVersion: "2.0.0"
ComponentDescription: Sample Image classification inference using DLR and amazon resnet50 default model.
ComponentPublisher: Amazon
ComponentDependencies:
  variant.DLR:
    VersionRequirement: "~1.3.0"
    DependencyType: HARD
  variant.ImageClassification.ModelStore:
    VersionRequirement: "~2.0.0"
    DependencyType: HARD
  aws.greengrass.Nucleus:
    VersionRequirement: "~2.0.0"
    DependencyType: SOFT
ComponentConfiguration:
  DefaultConfiguration:
    Accelerator: "cpu"
    MLRootPath: "$HOME/greengrass_ml"
    ImageName: "cat.jpeg"
    InferenceInterval: 3600
    ModelResourceKey:
      armv7l: "DLR-resnet50-armv7l-cpu-ImageClassification"
      x86_64: "DLR-resnet50-x86_64-cpu-ImageClassification"
Manifests:
  - Platform:
      os: linux
      architecture: arm
    Name: 32-bit armv7l - Linux (raspberry pi)
    Artifacts:
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/image_classification.zip
        Unarchive: ZIP
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/init.sh
    Lifecycle:
      Install:
        RequiresPrivilege: true
        script: |-
          bash {artifacts:path}/init.sh {artifacts:decompressedPath}/image_classification/sample_images {configuration:/MLRootPath}
          bash {variant.DLR:artifacts:decompressedPath}/installer/installer.sh -a {configuration:/Accelerator} -p {configuration:/MLRootPath}
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
  - Platform:
      os: linux
      architecture: amd64
    Name: 64-bit x86_64 - Linux (ubuntu, deeplens, amazon linux 2)
    Artifacts:
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/image_classification.zip
        Unarchive: ZIP
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/init.sh
    Lifecycle:
      Install:
        RequiresPrivilege: true
        script: |-
          bash {artifacts:path}/init.sh {artifacts:decompressedPath}/image_classification/sample_images {configuration:/MLRootPath}
          bash {variant.DLR:artifacts:decompressedPath}/installer/installer.sh -a {configuration:/Accelerator} -p {configuration:/MLRootPath} -e {variant.DLR:artifacts:path}/environment.yaml
        timeout: 900
      Run:
        RequiresPrivilege: true
        script: |-
          export PATH="{configuration:/MLRootPath}/greengrass_ml_dlr_conda/bin:$PATH"
          eval "$({configuration:/MLRootPath}/greengrass_ml_dlr_conda/bin/conda shell.bash hook)"
          conda activate greengrass_ml_dlr_conda
          python3 {artifacts:decompressedPath}/image_classification/inference.py -a {configuration:/Accelerator} -m {variant.ImageClassification.ModelStore:artifacts:decompressedPath}/{configuration:/ModelResourceKey/x86_64} -p {configuration:/MLRootPath} -i {configuration:/ImageName} -s {configuration:/InferenceInterval}
      shutdown:
        RequiresPrivilege: true
        script: |-
          export PATH="{configuration:/MLRootPath}/greengrass_ml_dlr_conda/bin:$PATH"
          eval "$({configuration:/MLRootPath}/greengrass_ml_dlr_conda/bin/conda shell.bash hook)"
          conda deactivate
```

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "aws.greengrass.DLRImageClassification",
  "ComponentVersion": "2.0.0",
  "ComponentDescription": "Sample Image classification inference using DLR and amazon resnet50 default model.",
  "ComponentPublisher": "Amazon",
  "ComponentDependencies": {
    "variant.DLR": {
      "VersionRequirement": "~1.3.0",
      "DependencyType": "HARD"
    },
    "variant.ImageClassification.ModelStore": {
      "VersionRequirement": "~2.0.0",
      "DependencyType": "HARD"
    },
    "aws.greengrass.Nucleus": {
      "VersionRequirement": "~2.0.0",
      "DependencyType": "SOFT"
    }
  },
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
  },
  "Manifests": [
    {
      "Platform": {
        "os": "linux",
        "architecture": "arm"
      },
      "Name": "32-bit armv7l - Linux (raspberry pi)",
      "Artifacts": [
        {
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/image_classification.zip",
          "Unarchive": "ZIP"
        },
        {
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/init.sh"
        }
      ],
      "Lifecycle": {
        "Install": {
          "RequiresPrivilege": true,
          "script": "bash {artifacts:path}/init.sh {artifacts:decompressedPath}/image_classification/sample_images {configuration:/MLRootPath}\nbash {variant.DLR:artifacts:decompressedPath}/installer/installer.sh -a {configuration:/Accelerator} -p {configuration:/MLRootPath}",
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
    },
    {
      "Platform": {
        "os": "linux",
        "architecture": "amd64"
      },
      "Name": "64-bit x86_64 - Linux (ubuntu, deeplens, amazon linux 2)",
      "Artifacts": [
        {
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/image_classification.zip",
          "Unarchive": "ZIP"
        },
        {
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/init.sh"
        }
      ],
      "Lifecycle": {
        "Install": {
          "RequiresPrivilege": true,
          "script": "bash {artifacts:path}/init.sh {artifacts:decompressedPath}/image_classification/sample_images {configuration:/MLRootPath}\nbash {variant.DLR:artifacts:decompressedPath}/installer/installer.sh -a {configuration:/Accelerator} -p {configuration:/MLRootPath} -e {variant.DLR:artifacts:path}/environment.yaml",
          "timeout": 900
        },
        "Run": {
          "RequiresPrivilege": true,
          "script": "export PATH=\"{configuration:/MLRootPath}/greengrass_ml_dlr_conda/bin:$PATH\"\neval \"$({configuration:/MLRootPath}/greengrass_ml_dlr_conda/bin/conda shell.bash hook)\"\nconda activate greengrass_ml_dlr_conda\npython3 {artifacts:decompressedPath}/image_classification/inference.py -a {configuration:/Accelerator} -m {variant.ImageClassification.ModelStore:artifacts:decompressedPath}/{configuration:/ModelResourceKey/x86_64} -p {configuration:/MLRootPath} -i {configuration:/ImageName} -s {configuration:/InferenceInterval}"
        },
        "shutdown": {
          "RequiresPrivilege": true,
          "script": "export PATH=\"{configuration:/MLRootPath}/greengrass_ml_dlr_conda/bin:$PATH\"\neval \"$({configuration:/MLRootPath}/greengrass_ml_dlr_conda/bin/conda shell.bash hook)\"\nconda deactivate"
        }
      }
    }
  ]
}
```

------
# Image Classification Model Store<a name="variant-image-classification-component"></a>

The Image Classification Model Store component \(`variant.ImageClassification.ModelStore`\) is a data component that contains [sample `resnet-50` models](https://cv.gluon.ai/model_zoo/classification.html#resnet) as Greengrass artifacts\. The pre\-trained models used in this component are fetched from the [GluonCV Model Zoo](https://cv.gluon.ai/model_zoo/index.html) and are compiled using Amazon SageMaker Neo Deep Learning Runtime\.

The [DLR Image Classification](dlr-image-classification-component.md) inference component uses this component as a dependency for the model source\. To use your own model with the inference component, you can [create a private version](ml-customization.md) of this variant component\. You can also use the component recipe of this component as a template to create your own private model component\. 

This component has the following versions:
+ 2\.0\.x

## Requirements<a name="variant-image-classification-component-requirements"></a><a name="dlr-supported-platforms"></a>

This component requires one of the following devices configured for use with AWS IoT Greengrass\. For more information see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.
+ **32\-bit Armv7l**

  [Raspberry Pi](https://www.raspberrypi.org) running Raspberry Pi OS, 2020\-08\-24\.
+ **64\-bit x86\_64**

  Device running Amazon Linux 2 or Ubuntu 18\.04, with [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later\.

## Component recipe<a name="variant-image-classification-component-recipe"></a><a name="view-component-recipe"></a>

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
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "variant.ImageClassification.ModelStore",
  "ComponentVersion": "2.0.0",
  "ComponentDescription": "Downloads ml models to the device as artifacts.",
  "ComponentPublisher": "Amazon",
  "ComponentDependencies": {
    "aws.greengrass.Nucleus": {
      "VersionRequirement": "~2.0.0",
      "DependencyType": "SOFT"
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
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/DLR-resnet50-armv7l-cpu-ImageClassification.zip",
          "Unarchive": "ZIP"
        }
      ]
    },
    {
      "Platform": {
        "os": "linux",
        "architecture": "amd64"
      },
      "Name": "64-bit x86_64 - Linux (ubuntu, deeplens, amazon linux 2)",
      "Artifacts": [
        {
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/DLR-resnet50-x86_64-cpu-ImageClassification.zip",
          "Unarchive": "ZIP"
        }
      ]
    }
  ]
}
```

------
#### [ YAML ]

```
RecipeFormatVersion: "2020-01-25"
ComponentName: variant.ImageClassification.ModelStore
ComponentVersion: "2.0.0"
ComponentDescription: Downloads ml models to the device as artifacts.
ComponentPublisher: Amazon
ComponentDependencies:
  aws.greengrass.Nucleus:
    VersionRequirement: "~2.0.0"
    DependencyType: SOFT
Manifests:
  - Platform:
      os: linux
      architecture: arm
    Name: 32-bit armv7l - Linux (raspberry pi)
    Artifacts:
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/DLR-resnet50-armv7l-cpu-ImageClassification.zip
        Unarchive: ZIP
  - Platform:
      os: linux
      architecture: amd64
    Name: 64-bit x86_64 - Linux (ubuntu, deeplens, amazon linux 2)
    Artifacts:
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/DLR-resnet50-x86_64-cpu-ImageClassification.zip
        Unarchive: ZIP
```

------
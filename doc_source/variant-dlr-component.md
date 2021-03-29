# DLR Installer<a name="variant-dlr-component"></a>

The DLR Installer component \(`variant.DLR`\) is a data component that contains an installation script to install the Neo\-AI\-DLR 1\.3\.0 runtime and its dependencies in a virtual environment on your device\. The [DLR Image Classification](dlr-image-classification-component.md) and [DLR Object Detection](dlr-object-detection-component.md) component use this component as a dependency for installing DLR\. 

This component has the following versions:
+ 1\.3\.x

## Requirements<a name="variant-dlr-component-requirements"></a><a name="dlr-supported-platforms"></a>

The public machine learning inference components require a Greengrass core device running one of the following supported platforms\. For more information see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.
+ **32\-bit Armv7l**

  [Raspberry Pi](https://www.raspberrypi.org) running Raspberry Pi OS, 2020\-08\-24\.
+ **64\-bit x86\_64**

  Device running Amazon Linux 2 or Ubuntu 18\.04, with [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later\.

## Usage<a name="variant-dlr-component-usage"></a>

The DLR Installer sets up a virtual environment on your device that includes the OpenCV and NumPy libraries that are required for DLR\. 

**Note**  
The installer script in this component also installs the latest versions of additional system libraries that are required to configure the virtual environment and to run DLR on your device\. This might upgrade the existing system libraries on your device\. Make sure you review the following table for the list of libraries that this component installs for each supported operating system\.


| Operating system | Libraries installed on the device system | Libraries installed in the virtual environment | 
| --- | --- | --- | 
| Raspberry Pi OS | build\-essential, cmake, ca\-certificates, git | setuptools, wheel | 
| Amazon Linux 2 | mesa\-libGL | None | 
| Ubuntu | wget | None | 

When you deploy your inference component, this runtime component first verifies if your device already has DLR and its dependencies installed, and if not, then installs them for you\. 

To use runtimes other than DLR, you can use the component recipe of this component as a template to create a private runtime component for your runtime\. 

## Component recipe<a name="variant-dlr-component-recipe"></a><a name="view-component-recipe"></a>

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

The following excerpt shows the component recipe for version 1\.3\.0 of the component\. 

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "variant.DLR",
  "ComponentVersion": "1.3.0",
  "ComponentDescription": "Runtime for deep learning models and decision tree models compiled by AWS SageMaker Neo, TVM, or Treelite.",
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
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/installer.zip",
          "Unarchive": "ZIP"
        },
        {
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/amd64/environment.yaml"
        }
      ]
    },
    {
      "Platform": {
        "os": "linux",
        "architecture": "amd64"
      },
      "Name": "64-bit x86_64 - Linux (ubuntu, deeplens, amazon linux 2 with GLIBC version >= 2.27)",
      "Artifacts": [
        {
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/installer.zip",
          "Unarchive": "ZIP"
        },
        {
          "URI": "s3://$bucketName$/$sampleArtifactsDirectory$/amd64/environment.yaml"
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
ComponentName: variant.DLR
ComponentVersion: "1.3.0"
ComponentDescription: Runtime for deep learning models and decision tree models compiled by AWS SageMaker Neo, TVM, or Treelite.
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
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/installer.zip
        Unarchive: ZIP
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/amd64/environment.yaml
  - Platform:
      os: linux
      architecture: amd64
    Name: 64-bit x86_64 - Linux (ubuntu, deeplens, amazon linux 2 with GLIBC version >= 2.27)
    Artifacts:
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/installer.zip
        Unarchive: ZIP
      - URI: s3://$bucketName$/$sampleArtifactsDirectory$/amd64/environment.yaml
```

------
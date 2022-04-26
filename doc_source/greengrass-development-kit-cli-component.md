# component<a name="greengrass-development-kit-cli-component"></a>

Use the `component` command in the AWS IoT Greengrass Development Kit Command\-Line Interface \(GDK CLI\) to create, build, and publish custom Greengrass components\.

**Topics**
+ [init](#greengrass-development-kit-cli-component-init)
+ [build](#greengrass-development-kit-cli-component-build)
+ [publish](#greengrass-development-kit-cli-component-publish)
+ [list](#greengrass-development-kit-cli-component-list)

## init<a name="greengrass-development-kit-cli-component-init"></a>

Initialize a Greengrass component folder from a component template or community component\.

<a name="gdk-cli-component-templates-community-components"></a>The GDK CLI retrieves community components from the [Greengrass Software Catalog](greengrass-software-catalog.md) and component templates from the [AWS IoT Greengrass Component Templates repository on GitHub](https://github.com/aws-greengrass/aws-greengrass-component-templates)\.

**Note**  
<a name="gdk-cli-component-init-empty-folder-requirement"></a>If you use GDK CLI v1\.0\.0, you must run this command in an empty folder\. The GDK CLI downloads the template or community component to the current folder\.  
<a name="gdk-cli-component-init-empty-folder-requirement-gdk-cli-v1.1.0"></a>If you use GDK CLI v1\.1\.0 or later, you can specify the `--name` argument to specify the folder where the GDK CLI downloads the template or community component\. If you use this argument, specify a folder that doesn't exist\. The GDK CLI creates the folder for you\. If you don't specify this argument, the GDK CLI uses the current folder, which must be empty\.  
If the component uses the [zip build system](gdk-cli-configuration-file.md#gdk-cli-configuration-file-build-system), the GDK CLI zips certain files in the component's folder into a zip file with the same name as the component folder\. For example, if the component folder's name is `HelloWorld`, the GDK CLI creates a zip file named `HelloWorld.zip`\. In the component recipe, the zip artifact name must match the name of the component folder\. If you use GDK CLI version 1\.0\.0 on a Windows device, the component folder and zip file names must contain only lowercase letters\.  
If you initialize a template or community component that uses the zip build system to a folder with a different name than the template or component, you must change the zip artifact name in the component recipe\. Update the `Artifacts` and `Lifecycle` definitions such that the zip file name matches the name of the component folder\. The following example highlights the zip file name in the `Artifacts` and `Lifecycle` definitions\.  

```
{
  ...
  "Manifests": [
    {
      "Platform": {
        "os": "all"
      },
      "Artifacts": [
        {
          "URI": "s3://{BUCKET_NAME}/{COMPONENT_NAME}/{COMPONENT_VERSION}/HelloWorld.zip",
          "Unarchive": "ZIP"
        }
      ],
      "Lifecycle": {
        "Run": "python3 -u {artifacts:decompressedPath}/HelloWorld/main.py {configuration:/Message}"
      }
    }
  ]
}
```

```
---
...
Manifests:
  - Platform:
      os: all
    Artifacts:
      - URI: "s3://BUCKET_NAME/COMPONENT_NAME/COMPONENT_VERSION/HelloWorld.zip"
        Unarchive: ZIP
    Lifecycle:
      Run: "python3 -u {artifacts:decompressedPath}/HelloWorld/main.py {configuration:/Message}"
```

**Synopsis**  

```
$ gdk component init
    [--language]
    [--template]
    [--repository]
    [--name]
```

**Arguments \(initialize from component template\)**  
+ `-l`, `--language` – The programming language to use for the template that you specify\.

  You must specify either `--repository` or `--language` and `--template`\.
+ `-t`, `--template` – The component template to use for a local component project\. To view available templates, use the [list](#greengrass-development-kit-cli-component-list) command\.

  You must specify either `--repository` or `--language` and `--template`\.
+ `-n`, `--name` – \(Optional\) The name of the local folder where the GDK CLI initializes the component\. Specify a folder that doesn't exist\. The GDK CLI creates the folder for you\.

  This feature is available for GDK CLI v1\.1\.0 and later\.

**Arguments \(initialize from community component\)**  
+ `-r`, `--repository` – The community component to check out into the local folder\. To view available community components, use the [list](#greengrass-development-kit-cli-component-list) command\.

  You must specify either `--repository` or `--language` and `--template`\.
+ `-n`, `--name` – \(Optional\) The name of the local folder where the GDK CLI initializes the component\. Specify a folder that doesn't exist\. The GDK CLI creates the folder for you\.

  This feature is available for GDK CLI v1\.1\.0 and later\.

**Output**  
The following example shows the output produced when you run this command to initialize a component folder from the Python Hello World template\.  

```
$ gdk component init -l python -t HelloWorld
[2021-11-29 12:51:40] INFO - Initializing the project directory with a python component template - 'HelloWorld'.
[2021-11-29 12:51:40] INFO - Fetching the component template 'HelloWorld-python' from Greengrass Software Catalog.
```
The following example shows the output produced when you run this command to initialize a component folder from a community component\.  

```
$ gdk component init -r aws-greengrass-labs-database-influxdb
[2022-01-24 15:44:33] INFO - Initializing the project directory with a component from repository catalog - 'aws-greengrass-labs-database-influxdb'.
[2022-01-24 15:44:33] INFO - Fetching the component repository 'aws-greengrass-labs-database-influxdb' from Greengrass Software Catalog.
```

## build<a name="greengrass-development-kit-cli-component-build"></a>

Build a component's source into a recipe and artifacts that you can publish to the AWS IoT Greengrass service\. The GDK CLI runs the build system that you specify in the [GDK CLI configuration file](gdk-cli-configuration-file.md), `gdk-config.json`\. You must run this command in the same folder where the `gdk-config.json` file exists\.

When you run this command, the GDK CLI creates a recipe and artifacts in the `greengrass-build` folder in the component folder\. The GDK CLI saves the recipe in the `greengrass-build/recipes` folder and saves the artifacts in the `greengrass-build/artifacts/componentName/componentVersion` folder\.

If you use GDK CLI v1\.1\.0 or later, the component recipe can specify artifacts that exist in an S3 bucket but not in the local component build folder\. You can use this feature to reduce bandwidth usage when you develop components with large artifacts, such as machine learning models\.

After you build a component, you can do one of the following to test it on a Greengrass core device:
+ If you develop on a different device than where you run the AWS IoT Greengrass Core software, you must publish the component to deploy it to a Greengrass core device\. Publish the component to the AWS IoT Greengrass service, and deploy it to the Greengrass core device\. For more information, see the [publish](#greengrass-development-kit-cli-component-build) command and [Create deployments](create-deployments.md)\.
+ If you develop on the same device where you run the AWS IoT Greengrass Core software, you can publish the component to the AWS IoT Greengrass service to deploy, or you can create a local deployment to install and run the component\. To create a local deployment, use the Greengrass CLI\. For more information, see [Greengrass Command Line Interface](gg-cli.md) and [Test AWS IoT Greengrass components with local deployments](test-components.md)\. When you create the local deployment, specify `greengrass-build/recipes` as the recipes folder and `greengrass-build/artifacts` as the artifacts folder\.

**Synopsis**  

```
$ gdk component build
```

**Arguments**  
None

**Output**  
The following example shows the output produced when you run this command\.  

```
$ gdk component build
[2021-11-29 13:18:49] INFO - Getting project configuration from gdk-config.json
[2021-11-29 13:18:49] INFO - Found component recipe file 'recipe.yaml' in the  project directory.
[2021-11-29 13:18:49] INFO - Building the component 'com.example.PythonHelloWorld' with the given project configuration.
[2021-11-29 13:18:49] INFO - Using 'zip' build system to build the component.
[2021-11-29 13:18:49] WARNING - This component is identified as using 'zip' build system. If this is incorrect, please exit and specify custom build command in the 'gdk-config.json'.
[2021-11-29 13:18:49] INFO - Zipping source code files of the component.
[2021-11-29 13:18:49] INFO - Copying over the build artifacts to the greengrass component artifacts build folder.
[2021-11-29 13:18:49] INFO - Updating artifact URIs in the recipe.
[2021-11-29 13:18:49] INFO - Creating component recipe in 'C:\Users\MyUser\Documents\greengrass-components\python\HelloWorld\greengrass-build\recipes'.
```

## publish<a name="greengrass-development-kit-cli-component-publish"></a>

Publish this component to the AWS IoT Greengrass service\. This command uploads build artifacts to an S3 bucket, updates the artifact URI in the recipe, and creates a new version of component from the recipe\. The GDK CLI uses the S3 bucket and AWS Region that you specify in the [GDK CLI configuration file](gdk-cli-configuration-file.md), `gdk-config.json`\. You must run this command in the same folder where the `gdk-config.json` file exists\.

<a name="gdk-cli-s3-bucket-name-formation"></a>If you use GDK CLI v1\.1\.0 or later, you can specify the `--bucket` argument to specify the S3 bucket where the GDK CLI uploads the component's artifacts\. <a name="gdk-cli-s3-bucket-name-formation-format"></a>If you don't specify this argument, the GDK CLI uploads to the S3 bucket whose name is `bucketPrefix-region-accountId`, where *bucketPrefix* and *region* are the values that you specify in `gdk-config.json`, and *accountId* is your AWS account ID\. The GDK CLI creates the bucket if it doesn't exist\.

When you run this command, the GDK CLI publishes the component with the version that you specify in the recipe\. If you specify `NEXT_PATCH`, the GDK CLI uses the next patch version that doesn't already exist\. Semantic versions use a *major*\.*minor*\.*patch* numbering system\. For more information, see the [semantic version specification](https://semver.org/)\.

**Note**  
If you use GDK CLI v1\.1\.0 or later, when you run this command, the GDK CLI checks if the component is built\. If the component isn't built, the GDK CLI [builds the component](#greengrass-development-kit-cli-component-build) before it publishes the component\.

**Synopsis**  

```
$ gdk component publish
    [--bucket]
```

**Arguments**  
+ `-b`, `--bucket` – \(Optional\) Specify the name of the S3 bucket where the GDK CLI publishes component artifacts\.

  <a name="gdk-cli-s3-bucket-name-formation-format"></a>If you don't specify this argument, the GDK CLI uploads to the S3 bucket whose name is `bucketPrefix-region-accountId`, where *bucketPrefix* and *region* are the values that you specify in `gdk-config.json`, and *accountId* is your AWS account ID\.

  The GDK CLI creates the bucket if it doesn't exist\.

  This feature is available for GDK CLI v1\.1\.0 and later\.

**Output**  
The following example shows the output produced when you run this command\.  

```
$ gdk component publish
[2021-11-29 13:45:29] INFO - Getting project configuration from gdk-config.json
[2021-11-29 13:45:29] INFO - Found component recipe file 'recipe.yaml' in the  project directory.
[2021-11-29 13:45:29] INFO - Found credentials in shared credentials file: ~/.aws/credentials
[2021-11-29 13:45:30] INFO - Publishing the component 'com.example.PythonHelloWorld' with the given project configuration.
[2021-11-29 13:45:30] INFO - No private version of the component 'com.example.PythonHelloWorld' exist in the account. Using '1.0.0' as the next version to create.
[2021-11-29 13:45:30] INFO - Uploading the component built artifacts to s3 bucket.
[2021-11-29 13:45:30] INFO - Uploading component artifacts to S3 bucket: {bucket}. If this is your first time using this bucket, add the 's3:GetObject' permission to each core device's token exchange role to allow it to download the component artifacts. For more information, see https://docs.aws.amazon.com/greengrass/v2/developerguide/device-service-role.html.
[2021-11-29 13:45:30] INFO - Not creating an artifacts bucket as it already exists.
[2021-11-29 13:45:30] INFO - Updating the component recipe com.example.PythonHelloWorld-1.0.0.
[2021-11-29 13:45:30] INFO - Creating a new greengrass component com.example.PythonHelloWorld-1.0.0
[2021-11-29 13:45:30] INFO - Created private version '1.0.0' of the component in the account.'com.example.PythonHelloWorld'.
```

## list<a name="greengrass-development-kit-cli-component-list"></a>

Retrieve the list of available component templates and community components\.

<a name="gdk-cli-component-templates-community-components"></a>The GDK CLI retrieves community components from the [Greengrass Software Catalog](greengrass-software-catalog.md) and component templates from the [AWS IoT Greengrass Component Templates repository on GitHub](https://github.com/aws-greengrass/aws-greengrass-component-templates)\.

You can pass the output of this command to the [init](#greengrass-development-kit-cli-component-init) command to initialize component repositories from templates and community components\.

**Synopsis**  

```
$ gdk component list
    [--template]
    [--repository]
```

**Arguments**  
+ `-t`, `--template` – \(Optional\) Specify this argument to list available component templates\. This command outputs the name and language of each template in the format `name-language`\. For example, in `HelloWorld-python`, the template name is `HelloWorld` and the language is `python`\.
+ `-r`, `--repository` – \(Optional\) Specify this argument to list available community component repositories\.

**Output**  
The following example shows the output produced when you run this command\.  

```
$ gdk component list --template
[2021-11-29 12:29:04] INFO - Listing all the available component templates from Greengrass Software Catalog.
[2021-11-29 12:29:04] INFO - Found '2' component templates to display.
1. HelloWorld-python
2. HelloWorld-java
```
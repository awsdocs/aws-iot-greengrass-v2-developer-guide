# Greengrass Development Kit CLI configuration file<a name="gdk-cli-configuration-file"></a>

The AWS IoT Greengrass Development Kit Command\-Line Interface \(GDK CLI\) reads from a configuration file named `gdk-config.json` to build and publish components\. This configuration file must exist in the root of the component repository\. You can use the GDK CLI [init command](greengrass-development-kit-cli-component.md#greengrass-development-kit-cli-component-init) to initialize component repositories with this configuration file\.

**Topics**
+ [GDK CLI configuration file format](#gdk-config-format)
+ [GDK CLI configuration file examples](#gdk-config-examples)

## GDK CLI configuration file format<a name="gdk-config-format"></a>

When you define a GDK CLI configuration file for a component, you specify the following information in JSON format\.

`gdk_version`  
The minimum version of the GDK CLI that is compatible with this component\.

`component`  
The configuration for this component\.    
`componentName`    
`author`  
The author or publisher of the component\.  
`version`  
The version of the component\. Specify one of the following:  <a name="gdk-cli-configuration-file-component-version-options"></a>
+ `NEXT_PATCH` – When you choose this option, the GDK CLI sets the version when you publish the component\. The GDK CLI queries the AWS IoT Greengrass service to identify the latest published version of the component\. Then, it sets the version to the next patch version after that version\. If you haven't published the component before, the GDK CLI uses version `1.0.0`\.

  If you choose this option, you can't use the [Greengrass CLI](greengrass-cli-component.md) to locally deploy and test the component to your local development computer that runs the AWS IoT Greengrass Core software\. To enable local deployments, you must specify a semantic version instead\.
+ A semantic version, such as **1\.0\.0**\. Semantic versions use a *major*\.*minor*\.*patch* numbering system\. For more information, see the [semantic version specification](https://semver.org/)\.

  If you develop components on a Greengrass core device where you want to deploy and test the component, choose this option\. You must build the component with a specific version to create local deployments with the [Greengrass CLI](greengrass-cli-component.md)\.  
`build`  
The configuration to use to build this component's source into artifacts\. This object contains the following information:    
  `build_system`   
The build system to use\. Choose from the following options:  <a name="gdk-cli-configuration-file-component-build-system-options"></a>
+ `zip` – Packages the component's folder into a ZIP file to define as the component's only artifact\. Choose this option for the following types of components:
  + Components that use interpreted programming languages, such as Python or JavaScript\.
  + Components that package files other than code, such as machine learning models or other resources\.

  The GDK CLI zips the component's folder into a zip file with the same name as the component folder\. For example, if the component folder's name is `HelloWorld`, the GDK CLI creates a zip file named `HelloWorld.zip`\.
**Note**  
If you use GDK CLI version 1\.0\.0 on a Windows device, the component folder and zip file names must contain only lowercase letters\.

  When the GDK CLI zips the component's folder into a zip file, it skips the following files:
  + The `gdk-config.json` file
  + The recipe file \(`recipe.json` or `recipe.yaml`\)
  + Build folders, such as `greengrass-build`
+ `maven` – Runs the `mvn clean package` command to build the component's source into artifacts\. Choose this option for components that use [Maven](https://maven.apache.org/), such as Java components\.

  On Windows devices, this feature is available for GDK CLI v1\.1\.0 and later\.
+ `gradle` – Runs the `gradle build` command to build the component's source into artifacts\. Choose this option for components that use [Gradle](https://gradle.org/)\. This feature is available for GDK CLI v1\.1\.0 and later\.

  The `gradle` build system supports Kotlin DSL as the build file\. This feature is available for GDK CLI v1\.2\.0 and later\.
+ `gradlew` – Runs the `gradlew` command to build the component's source into artifacts\. Choose this option for components that use the [Gradle Wrapper ](https://docs.gradle.org/current/userguide/gradle_wrapper.html)\.

  This feature is available for GDK CLI v1\.2\.0 and later\.
+ `custom` – Runs a custom command to build the component's source into a recipe and artifacts\. Specify the custom command in the `custom_build_command` parameter\.  
`custom_build_command`  
\(Optional\) The custom build command to run for a custom build system\. You must specify this parameter if you specify `custom` for `build_system`\.  
This command must create a recipe and artifacts in the following folders within the component folder\. The GDK CLI creates these folders for you when you run the [component build command](greengrass-development-kit-cli-component.md#greengrass-development-kit-cli-component-build)\.  
+ Recipe folder: `greengrass-build/recipes`
+ Artifacts folder: `greengrass-build/artifacts/componentName/componentVersion`

  Replace *componentName* with the component name, and replace *componentVersion* with the component version or `NEXT_PATCH`\.
You can specify a single string or a list of strings, where each string is a word in the command\. For example, to run a custom build command for a C\+\+ component, you might specify **cmake \-\-build build \-\-config Release** or **\["cmake", "\-\-build", "build", "\-\-config", "Release"\]**\.  
To view an example of a custom build system, see the [aws\.greengrass\.labs\.LocalWebServer community component on GitHub](https://github.com/awslabs/aws-greengrass-labs-local-web-server)\.  
`options`  
\(Optional\) Additional configuration options used during component build process\.  
This feature is available for GDK CLI v1\.2\.0 and later\.    
`excludes`  
A list of file patterns that define the files that should be excluded from the zip file\. Only valid when the `build_system` is `zip`\.  
The following files are always excluded from the zip file:  
+ The `gdk-config.json` file
+ The recipe file \(`recipe.json` or `recipe.yamal`\)
+ Build folders such as `greengrass-build`  
`publish`  
The configuration to use to publish this component to the AWS IoT Greengrass service\.  
<a name="gdk-cli-s3-bucket-name-formation"></a>If you use GDK CLI v1\.1\.0 or later, you can specify the `--bucket` argument to specify the S3 bucket where the GDK CLI uploads the component's artifacts\. <a name="gdk-cli-s3-bucket-name-formation-format"></a>If you don't specify this argument, the GDK CLI uploads to the S3 bucket whose name is `bucket-region-accountId`, where *bucket* and *region* are the values that you specify in `gdk-config.json`, and *accountId* is your AWS account ID\. The GDK CLI creates the bucket if it doesn't exist\.  
This object contains the following information:    
`bucket`  
The S3 bucket name to use to host component artifacts\.  
`region`  
The AWS Region where the GDK CLI publishes this component\.  
`options`  
\(Optional\) Additional configuration options used during component version creation\.  
This feature is available for GDK CLI v1\.2\.0 and later\.    
`file_upload_args`  
A JSON structure containing arguments sent to Amazon S3 while uploading files to a bucket, such as metadata and encryption mechanisms\. For a list of the allowed arguments, see the [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/customizations/s3.html#boto3.s3.transfer.S3Transfer.ALLOWED_UPLOAD_ARGS](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/customizations/s3.html#boto3.s3.transfer.S3Transfer.ALLOWED_UPLOAD_ARGS) class in the *Boto3 documentation\.*\.

## GDK CLI configuration file examples<a name="gdk-config-examples"></a>

You can reference the following GDK CLI configuration file examples to help you configure Greengrass component environments\.

### Hello World \(Python\)<a name="gdk-config-example-hello-world-python"></a>

The following GDK CLI configuration file supports a Hello World component that runs a Python script\. This configuration file uses the `zip` build system to package the component's Python script into a ZIP file that the GDK CLI uploads as an artifact\.

```
{
  "component": {
    "com.example.PythonHelloWorld": {
      "author": "Amazon",
      "version": "NEXT_PATCH",
      "build": {
        "build_system" : "zip",
        "options": {
           "excludes": [".*"]
        }
      },
      "publish": {
        "bucket": "greengrass-component-artifacts",
        "region": "us-west-2",
        "options": {
           "file_upload_args": {
              "Metadata": 
                 "some-key" : "some-value"
              }
           }
        }
      }
    }
  },
  "gdk_version": "1.2.0"
}
```

### Hello World \(Java\)<a name="gdk-config-example-hello-world-java"></a>

The following GDK CLI configuration file supports a Hello World component that runs a Java application\. This configuration file uses the `maven` build system to package the component's Java source code into a JAR file that the GDK CLI uploads as an artifact\.

```
{
  "component": {
    "com.example.JavaHelloWorld": {
      "author": "Amazon",
      "version": "NEXT_PATCH",
      "build": {
        "build_system" : "maven"
      },
      "publish": {
        "bucket": "greengrass-component-artifacts",
        "region": "us-west-2",
        "options": {
           "file_upload_args": {
              "Metadata": 
                 "some-key" : "some-value"
              }
           }
        }
      }
    }
  },
  "gdk_version": "1.2.0"
}
```

### Community components<a name="gdk-config-community-component-examples"></a>

Several community components in the [Greengrass Software Catalog](greengrass-software-catalog.md) use the GDK CLI\. You can explore the GDK CLI configuration files in these components' repositories\.

**To view community components' GDK CLI configuration files**

1. Run the following command to list the community components that use the GDK CLI\.

   ```
   gdk component list --repository
   ```

   The response lists the name of the GitHub repository for each community component that uses the GDK CLI\. Each repository exists in the `awslabs` organization\.

   ```
   [2022-02-22 17:27:31] INFO - Listing all the available component repositories from Greengrass Software Catalog.
   [2022-02-22 17:27:31] INFO - Found '6' component repositories to display.
   1. aws-greengrass-labs-database-influxdb
   2. aws-greengrass-labs-telemetry-influxdbpublisher
   3. aws-greengrass-labs-dashboard-grafana
   4. aws-greengrass-labs-dashboard-influxdb-grafana
   5. aws-greengrass-labs-local-web-server
   6. aws-greengrass-labs-lookoutvision-gstreamer
   ```

1. Open a community component's GitHub repository at the following URL\. Replace *community\-component\-name* with the name of a community component from the previous step\.

   ```
   https://github.com/awslabs/community-component-name
   ```
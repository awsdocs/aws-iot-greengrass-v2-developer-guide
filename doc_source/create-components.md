# Create AWS IoT Greengrass components<a name="create-components"></a>

You can develop custom AWS IoT Greengrass components on a local development computer or a Greengrass core device\. AWS IoT Greengrass provides the [AWS IoT Greengrass Development Kit Command\-Line Interface \(GDK CLI\)](greengrass-development-kit-cli.md) to help you create, build, and publish components from predefined component templates and [community components](greengrass-software-catalog.md)\. You can also run built\-in shell commands to create, build, and publish components\. Choose from the following options to create custom Greengrass components:
+ **Use the Greengrass Development Kit CLI**

  Use the GDK CLI to develop components on a local development computer\. The GDK CLI builds and packages component source code into a recipe and artifacts that you can publish as a private component to the AWS IoT Greengrass service\. You can configure the GDK CLI to automatically update the component's version and artifact URIs when you publish the component, so you don't need to update the recipe each time\. To develop a component using the GDK CLI, you can start from a template or a community component from the [Greengrass Software Catalog](greengrass-software-catalog.md)\. For more information, see [AWS IoT Greengrass Development Kit Command\-Line Interface](greengrass-development-kit-cli.md)\.
+ **Run built\-in shell commands**

  You can run built\-in shell commands to develop components on a local development computer or on a Greengrass core device\. You use shell commands to copy or build component source code into artifacts\. Each time you create a new version of a component, you must create or update the recipe with the new component version\. When you publish the component to the AWS IoT Greengrass service, you must update the URI to each component artifact in the recipe\.

**Topics**
+ [Create a component \(GDK CLI\)](#create-component-gdk-cli)
+ [Create a component \(shell commands\)](#create-component-shell-commands)

## Create a component \(GDK CLI\)<a name="create-component-gdk-cli"></a>

Follow instructions in this section to create and build a component using the GDK CLI\.

**To develop a Greengrass component \(GDK CLI\)**

1. If you haven't already, install the GDK CLI on your development computer\. For more information, see [Install or update the AWS IoT Greengrass Development Kit Command\-Line Interface](install-greengrass-development-kit-cli.md)\.

1. Change to the folder where you want to create component folders\.

------
#### [ Linux or Unix ]

   ```
   mkdir ~/greengrassv2
   cd ~/greengrassv2
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   mkdir %USERPROFILE%\greengrassv2
   cd %USERPROFILE%\greengrassv2
   ```

------
#### [ PowerShell ]

   ```
   mkdir ~/greengrassv2
   cd ~/greengrassv2
   ```

------

1. Choose a component template or community component to download\. The GDK CLI downloads the template or community component, so you can start from a functional example\. Use the [component list](greengrass-development-kit-cli-component.md#greengrass-development-kit-cli-component-list) command to retrieve the list of available templates or community components\.
   + To list component templates, run the following command\. Each line in the response includes a template's name and programming language\.

     ```
     gdk component list --template
     ```
   + To list community components, run the following command\.

     ```
     gdk component list --repository
     ```

1. Create and change to a component folder where the GDK CLI downloads the template or community component\. Replace *HelloWorld* with the name of the component, or another name that helps you identify this component folder\.

------
#### [ Linux or Unix ]

   ```
   mkdir HelloWorld
   cd HelloWorld
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   mkdir HelloWorld
   cd HelloWorld
   ```

------
#### [ PowerShell ]

   ```
   mkdir HelloWorld
   cd HelloWorld
   ```

------

1. Download the template or community component to the current folder\. Use the [component init](greengrass-development-kit-cli-component.md#greengrass-development-kit-cli-component-init) command\.
   + To create a component folder from a template, run the following command\. Replace *HelloWorld* with the name of the template, and replace *python* with the name of the programming language\.

     ```
     gdk component init --template HelloWorld --language python
     ```
   + To create a component folder from a community component, run the following command\. Replace *ComponentName* with the name of the community component\.

     ```
     gdk component init --repository ComponentName
     ```
**Note**  
<a name="gdk-cli-component-init-empty-folder-requirement"></a>If you use GDK CLI v1\.0\.0, you must run this command in an empty folder\. The GDK CLI downloads the template or community component to the current folder\.  
<a name="gdk-cli-component-init-empty-folder-requirement-gdk-cli-v1.1.0"></a>If you use GDK CLI v1\.1\.0 or later, you can specify the `--name` argument to specify the folder where the GDK CLI downloads the template or community component\. If you use this argument, specify a folder that doesn't exist\. The GDK CLI creates the folder for you\. If you don't specify this argument, the GDK CLI uses the current folder, which must be empty\.

1. The GDK CLI reads from the [GDK CLI configuration file](gdk-cli-configuration-file.md), named `gdk-config.json`, to build and publish components\. This configuration file exists in the root of the component folder\. The previous step creates this file for you\. In this step, you update `gdk-config.json` with information about your component\. Do the following:

   1. Open `gdk-config.json` in a text editor\.

   1. \(Optional\) Change the name of the component\. The component name is the key in the `component` object\.

   1. Change the author of the component\.

   1. \(Optional\) Change the version of the component\. Specify one of the following:<a name="gdk-cli-configuration-file-component-version-options"></a>
      + `NEXT_PATCH` – When you choose this option, the GDK CLI sets the version when you publish the component\. The GDK CLI queries the AWS IoT Greengrass service to identify the latest published version of the component\. Then, it sets the version to the next patch version after that version\. If you haven't published the component before, the GDK CLI uses version `1.0.0`\.

        If you choose this option, you can't use the [Greengrass CLI](greengrass-cli-component.md) to locally deploy and test the component to your local development computer that runs the AWS IoT Greengrass Core software\. To enable local deployments, you must specify a semantic version instead\.
      + A semantic version, such as **1\.0\.0**\. Semantic versions use a *major*\.*minor*\.*patch* numbering system\. For more information, see the [semantic version specification](https://semver.org/)\.

        If you develop components on a Greengrass core device where you want to deploy and test the component, choose this option\. You must build the component with a specific version to create local deployments with the [Greengrass CLI](greengrass-cli-component.md)\.

   1. \(Optional\) Change the build configuration for the component\. The build configuration defines how the GDK CLI builds the component's source into artifacts\. Choose from the following options for `build_system`:<a name="gdk-cli-configuration-file-component-build-system-options"></a>
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

   1. If you specify `custom` for `build_system`, add the `custom_build_command` to the `build` object\. In `custom_build_command`, specify a single string or list of strings, where each string is a word in the command\. For example, to run a custom build command for a C\+\+ component, you might specify **\["cmake", "\-\-build", "build", "\-\-config", "Release"\]**\.

   1. <a name="gdk-cli-s3-bucket-name-formation"></a>If you use GDK CLI v1\.1\.0 or later, you can specify the `--bucket` argument to specify the S3 bucket where the GDK CLI uploads the component's artifacts\. <a name="gdk-cli-s3-bucket-name-formation-format"></a>If you don't specify this argument, the GDK CLI uploads to the S3 bucket whose name is `bucket-region-accountId`, where *bucket* and *region* are the values that you specify in `gdk-config.json`, and *accountId* is your AWS account ID\. The GDK CLI creates the bucket if it doesn't exist\.

      Change the publish configuration for the component\. Do the following:

      1. Specify the name of the S3 bucket to use to host component artifacts\.

      1. Specify the AWS Region where the GDK CLI publishes the component\.

   When you're done with this step, the `gdk-config.json` file might look similar to the following example\.

   ```
   {
     "component": {
       "com.example.PythonHelloWorld": {
         "author": "Amazon",
         "version": "NEXT_PATCH",
         "build": {
           "build_system" : "zip"
         },
         "publish": {
           "bucket": "greengrass-component-artifacts",
           "region": "us-west-2"
         }
       }
     },
     "gdk_version": "1.0.0"
   }
   ```

1. Update the component recipe file, named `recipe.yaml` or `recipe.json`\. Do the following:

   1. If you downloaded a template or community component that uses the `zip` build system, check that the zip artifact name matches the name of the component folder\. The GDK CLI zips the component folder into a zip file with the same name as the component folder\. The recipe contains the zip artifact name in the list of component artifacts and in lifecycle scripts that use files in the zip artifact\. Update the `Artifacts` and `Lifecycle` definitions such that the zip file name matches the name of the component folder\. The following partial recipe examples highlight the zip file name in the `Artifacts` and `Lifecycle` definitions\.

------
#### [ JSON ]

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
                "URI": "s3://{COMPONENT_NAME}/{COMPONENT_VERSION}/HelloWorld.zip",
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

------
#### [ YAML ]

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

------

   1. \(Optional\) Update the component description, default configuration, artifacts, lifecycle scripts, and platform support\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.
**Important**  
When you use the GDK CLI, recipe attribute names are case sensitive and must match the capitalization in the [component recipe reference](component-recipe-reference.md)\.

   When you're done with this step, the recipe file might look similar to the following examples\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "{COMPONENT_NAME}",
     "ComponentVersion": "{COMPONENT_VERSION}",
     "ComponentDescription": "This is a simple Hello World component written in Python.",
     "ComponentPublisher": "{COMPONENT_AUTHOR}",
     "ComponentConfiguration": {
       "DefaultConfiguration": {
         "Message": "World"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "all"
         },
         "Artifacts": [
           {
             "URI": "s3://{COMPONENT_NAME}/{COMPONENT_VERSION}/HelloWorld.zip",
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

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: "2020-01-25"
   ComponentName: "{COMPONENT_NAME}"
   ComponentVersion: "{COMPONENT_VERSION}"
   ComponentDescription: "This is a simple Hello World component written in Python."
   ComponentPublisher: "{COMPONENT_AUTHOR}"
   ComponentConfiguration:
     DefaultConfiguration:
       Message: "World"
   Manifests:
     - Platform:
         os: all
       Artifacts:
         - URI: "s3://BUCKET_NAME/COMPONENT_NAME/COMPONENT_VERSION/HelloWorld.zip"
           Unarchive: ZIP
       Lifecycle:
         Run: "python3 -u {artifacts:decompressedPath}/HelloWorld/main.py {configuration:/Message}"
   ```

------

1. Develop and build the Greengrass component\. The [component build](greengrass-development-kit-cli-component.md#greengrass-development-kit-cli-component-build) command produces a recipe and artifacts in the `greengrass-build` folder in the component folder\. Run the following command\.

   ```
   gdk component build
   ```

When you're ready to test your component, use the GDK CLI to publish it to the AWS IoT Greengrass service\. Then, you can deploy the component to Greengrass core devices\. For more information, see [Publish components to deploy to your core devices](publish-components.md)\.

## Create a component \(shell commands\)<a name="create-component-shell-commands"></a>

Follow instructions in this section to create recipe and artifact folders that contain source code and artifacts for multiple components\.

**To develop a Greengrass component \(shell commands\)**

1. <a name="create-component-recipes-artifacts-folder-step"></a>Create a folder for your components with subfolders for recipes and artifacts\. Run the following commands on your Greengrass core device to create these folders and change to the component folder\. Replace *\~/greengrassv2* or *%USERPROFILE%\\greengrassv2* with the path to the folder to use for local development\.

------
#### [ Linux or Unix ]

   ```
   mkdir -p ~/greengrassv2/{recipes,artifacts}
   cd ~/greengrassv2
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   mkdir %USERPROFILE%\greengrassv2\recipes, %USERPROFILE%\greengrassv2\artifacts
   cd %USERPROFILE%\greengrassv2
   ```

------
#### [ PowerShell ]

   ```
   mkdir ~/greengrassv2/recipes, ~/greengrassv2/artifacts
   cd ~/greengrassv2
   ```

------

1. <a name="create-component-recipe-file-step"></a>Use a text editor to create a recipe file that defines your component's metadata, parameters, dependencies, lifecycle, and platform capability\. Include the component version in the recipe file name so that you can identify which recipe reflects which component version\. You can choose YAML or JSON format for your recipe\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

------
#### [ JSON ]

   ```
   nano recipes/com.example.HelloWorld-1.0.0.json
   ```

------
#### [ YAML ]

   ```
   nano recipes/com.example.HelloWorld-1.0.0.yaml
   ```

------
**Note**  
<a name="semver-para"></a>AWS IoT Greengrass uses semantic versions for components\. Semantic versions follow a *major*\.*minor*\.*patch* number system\. For example, version `1.0.0` represents the first major release for a component\. For more information, see the [semantic version specification](https://semver.org/)\.

1. Define the recipe for your component\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.

   Your recipe might look similar to the following Hello World example recipe\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.HelloWorld",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "My first AWS IoT Greengrass component.",
     "ComponentPublisher": "Amazon",
     "ComponentConfiguration": {
       "DefaultConfiguration": {
         "Message": "world"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Run": "python3 -u {artifacts:path}/hello_world.py \"{configuration:/Message}\""
         }
       },
       {
         "Platform": {
           "os": "windows"
         },
         "Lifecycle": {
           "Run": "py -3 -u {artifacts:path}/hello_world.py \"{configuration:/Message}\""
         }
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.HelloWorld
   ComponentVersion: '1.0.0'
   ComponentDescription: My first AWS IoT Greengrass component.
   ComponentPublisher: Amazon
   ComponentConfiguration:
     DefaultConfiguration:
       Message: world
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Run: |
           python3 -u {artifacts:path}/hello_world.py "{configuration:/Message}"
     - Platform:
         os: windows
       Lifecycle:
         Run: |
           py -3 -u {artifacts:path}/hello_world.py "{configuration:/Message}"
   ```

------

   This recipe runs a Hello World Python script, which might look similar to the following example script\.

   ```
   import sys
   
   message = "Hello, %s!" % sys.argv[1]
   
   # Print the message to stdout, which Greengrass saves in a log file.
   print(message)
   ```

1. Create a folder for the component version to develop\. We recommend that you use a separate folder for each component version's artifacts so that you can identify which artifacts are for each component version\. Run the following command\.

------
#### [ Linux or Unix ]

   ```
   mkdir -p artifacts/com.example.HelloWorld/1.0.0
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   mkdir artifacts/com.example.HelloWorld/1.0.0
   ```

------
#### [ PowerShell ]

   ```
   mkdir artifacts/com.example.HelloWorld/1.0.0
   ```

------
**Important**  <a name="local-artifact-folder-name-requirements"></a>
You must use the following format for the artifact folder path\. Include the component name and version that you specify in the recipe\.  

   ```
   artifacts/componentName/componentVersion/
   ```

1. Create the artifacts for your component in the folder that you created in the previous step\. Artifacts can include software, images, and any other binaries that your component uses\.

   When your component is ready, [test your component](test-components.md)\.
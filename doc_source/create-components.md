# Create local AWS IoT Greengrass components<a name="create-components"></a>

With AWS IoT Greengrass, you can develop and test components on your Greengrass core device\. To develop a new component or a new version of a component, create a recipe and artifacts for your new component version\.

**To develop a component on a Greengrass core device\.**

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
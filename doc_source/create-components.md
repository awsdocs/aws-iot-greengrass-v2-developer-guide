# Create custom AWS IoT Greengrass components<a name="create-components"></a>

With AWS IoT Greengrass, you can develop and test components on your Greengrass core device\.

**Topics**
+ [Develop a custom component](#develop-component)
+ [Test a custom component](#test-component)
+ [Interact with AWS services](interact-with-aws-services.md)
+ [Interprocess communication](interprocess-communication.md)
+ [Run AWS Lambda functions](run-lambda-functions.md)
+ [Run a Docker container](run-docker-container.md)
+ [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)

## Develop a custom component<a name="develop-component"></a>

To develop a new component or a new version of a component, create a recipe and artifacts for your new component version\.

**To develop a component on a Greengrass core device\.**

1. Create a folder for your component with subfolders for recipes and artifacts\. Run the following commands on your Greengrass core device to create these folders and change to the component folder\. Replace *\~/greengrassv2* with the path to the folder to use for local development\.

   ```
   mkdir -p ~/greengrassv2/{recipes,artifacts}
   cd ~/greengrassv2
   ```

1. Create a recipe that defines your component's metadata, parameters, dependencies, lifecycle, and platform capability\. Run the following command to create the recipe file and open it in the `nano` text editor\. Include the component version in the recipe file name so that you can identify which recipe reflects which component version\. You can choose YAML or JSON format for your recipe\.

------
#### [ YAML ]

   ```
   nano recipes/com.example.HelloWorld-1.0.0.yaml
   ```

------
#### [ JSON ]

   ```
   nano recipes/com.example.HelloWorld-1.0.0.json
   ```

------

1. Define the recipe for your component\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.

   Your recipe might look similar to the following Hello World example recipe\.

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: 2020-01-25
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
           python3 {artifacts:path}/hello_world.py '{configuration:/Message}'
   ```

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
           "Run": "python3 {artifacts:path}/hello_world.py '{configuration:/Message}'"
         }
       }
     ]
   }
   ```

------

   This recipe runs a Hello World Python script, which might look similar to the following example script\.

   ```
   import sys
   import datetime
   
   message = f"Hello, {sys.argv[1]}! Current time: {str(datetime.datetime.now())}."
   
   # Print the message to stdout.
   print(message)
   
   # Append the message to the log file.
   with open('/tmp/Greengrass_HelloWorld.log', 'a') as f:
       print(message, file=f)
   ```

1. Create a folder for the component version to develop\. We recommend that you use a separate folder for each component version's artifacts so that you can identify which artifacts are for each component version\. Run the following command\.

   ```
   mkdir -p artifacts/com.example.HelloWorld/1.0.0
   ```
**Important**  <a name="local-artifact-folder-name-requirements"></a>
You must use the following format for the artifact folder path\. Include the component name and version that you specify in the recipe\.  

   ```
   artifacts/componentName/componentVersion/
   ```

1. Create the artifacts for your component in the folder that you created in the previous step\. Artifacts can include software, images, and any other binaries that your component uses\.

   When your component is ready, [test your component](#test-component)\.

## Test a custom component<a name="test-component"></a>

When you are ready to test your component, you can install it to your Greengrass core device\.

**To test a component on an Greengrass core device**

1. The core device logs events such as component updates\. You can view this log file to discover and troubleshoot errors with your component, such as an invalid recipe\. This log file also displays messages that your component prints to standard out \(stdout\)\. We recommend that you open an additional terminal session on your core device to observe new log messages in real time\. Open a new terminal session, such as through SSH, and run the following command to view the logs\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

   ```
   sudo tail -f /greengrass/v2/logs/greengrass.log
   ```

   You can also view the log file for your component\.

   ```
   sudo tail -f /greengrass/v2/logs/com.example.HelloWorld.log
   ```

1. In your original terminal session, run the following command to update the core device with your component\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create \
     --recipeDir ~/greengrassv2/recipes \
     --artifactDir ~/greengrassv2/artifacts \
     --merge "com.example.HelloWorld=1.0.0"
   ```
**Note**  
You can also use the `greengrass-cli deployment create` command to set the value of your component's configuration parameters\. For more information, see [create](gg-cli-deployment.md#deployment-create)\.

1. Test your component as it runs on the Greengrass core device\. When you finish this version of your component, you can upload it to the AWS IoT Greengrass service\. Then, you can deploy the component to other core devices\. For more information, see [Upload components to deploy to your core devicesUpload components to deploy](upload-components.md)\.
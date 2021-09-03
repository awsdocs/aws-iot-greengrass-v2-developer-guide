# Create deployments<a name="create-deployments"></a>

You can create a deployment that targets a thing or thing group\.

When you create a deployment, you configure the software components to deploy and how the deployment job rolls out to target devices\. You can define the deployment in the JSON file that you provide to the AWS CLI\.

The deployment target determines the devices on which you want to run your components\. To deploy to one core device, specify a thing\. To deploy to multiple core devices, specify a thing group that includes those devices\. For more information about how to configure thing groups, see [Static thing groups](https://docs.aws.amazon.com/iot/latest/developerguide/thing-groups.html) and [Dynamic thing groups](https://docs.aws.amazon.com/iot/latest/developerguide/dynamic-thing-groups.html) in the *AWS IoT Developer Guide*\.

Follow the steps in this section to create a deployment to a target for the first time\. For more information about how to update the software components on a target that has a deployment, see [Revise deployments](revise-deployments.md)\.

**To create a deployment \(AWS CLI\)**

1. Create a file called `deployment.json`, and then copy the following JSON object into the file\. Replace *targetArn* with the ARN of the AWS IoT thing or thing group to target for the deployment\. Thing and thing group ARNs have the following format:
   + Thing: `arn:aws:iot:region:account-id:thing/thingName`
   + Thing group: `arn:aws:iot:region:account-id:thinggroup/thingGroupName`

   ```
   {
     "targetArn": "targetArn",
     "components": {
       
     }
   }
   ```

1. Add each component to deploy the target devices\. To do so, add key\-value pairs to the `components` object, where the key is the component name, and the value is an object that contains the details for that component\. Specify the following details for each component that you add:
   + `version` – The component version to deploy\.
   + `configurationUpdate` – The [configuration update](update-component-configurations.md) to deploy\. The update is a patch operation that modifies the component's existing configuration on each target device, or the component's default configuration if it doesn't exist on the target device\. You can specify the following configuration updates:
     + Reset updates \(`reset`\) – \(Optional\) A list of JSON pointers that define the configuration values to reset to their default values on the target device\. The AWS IoT Greengrass Core software applies reset updates before it applies merge updates\. For more information, see [Reset updates](update-component-configurations.md#reset-configuration-update)\.
     + Merge updates \(`merge`\) – \(Optional\) A JSON document that defines the configuration values to merge onto the target device\. You must serialize the JSON document as a string\. For more information, see [Merge updates](update-component-configurations.md#merge-configuration-update)\.
   + `runWith` – \(Optional\) The system process options that the AWS IoT Greengrass Core software uses to run this component's processes on the core device\. If you omit a parameter in the `runWith` object, the AWS IoT Greengrass Core software uses the default values that you configure on the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

     You can specify any of the following options:
     + `posixUser` – The POSIX system user and \(optional\) group to use to run this component's processes\. You can set the system user and group for generic and Lambda components\. Specify the user and group separated by a colon \(`:`\) in the following format: `user:group`\. If you don't specify a group, the AWS IoT Greengrass Core software uses the primary group for the user\. For more information, see [Configure the user and group that run components](configure-greengrass-core-v2.md#configure-component-user)\.
     + `systemResourceLimits` – The system resource limits to apply to this component's processes\. You can apply system resource limits to generic and non\-containerized Lambda components\. For more information, see [Configure system resource limits for components](configure-greengrass-core-v2.md#configure-component-system-resource-limits)\.

       You can specify any of the following options:
       + `cpus` – <a name="system-resource-limits-cpu-definition-this"></a>The maximum amount of CPU time that this component's processes can use on the core device\. A core device's total CPU time is equivalent to the device's number of CPU cores\. For example, on a core device with 4 CPU cores, you can set this value to `2` to limit this component's processes to 50 percent usage of each CPU core\. On a device with 1 CPU core, you can set this value to `0.25` to limit this component's processes to 25 percent usage of the CPU\. If you set this value to a number greater than the number of CPU cores, the AWS IoT Greengrass Core software doesn't limit the component's CPU usage\.
       + `memory` – <a name="system-resource-limits-memory-definition-this"></a>The maximum amount of RAM \(in kilobytes\) that this component's processes can use on the core device\.

       This feature is available for v2\.4\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.  
**Example basic configuration update**  

   The following example `components` object specifies to deploy a component, `com.example.PythonRuntime`, that expects a configuration parameter named `pythonVersion`\.

   ```
   {
     "targetArn": "targetArn",
     "components": {
       "com.example.PythonRuntime": {
         "componentVersion": "1.0.0",
         "configurationUpdate": {
           "merge": "{\"pythonVersion\":\"3.7\"}"
         }
       }
     }
   }
   ```  
**Example configuration update with reset and merge updates**  

   Consider an example industrial dashboard component, `com.example.IndustrialDashboard`, that has the following default configuration\.

   ```
   {
     "name": null,
     "mode": "REQUEST",
     "network": {
       "useHttps": true,
       "port": {
         "http": 80,
         "https": 443
       },
     },
     "tags": []
   }
   ```

   The following configuration update specifies the following instructions:

   1. Reset the HTTPS setting to its default value \(`true`\)\.

   1. Reset the list of industrial tags to an empty list\.

   1. Merge a list of industrial tags that identify temperature and pressure data streams for two boilers\.

   ```
   {
     "reset": [
       "/network/useHttps",
       "/tags"
     ],
     "merge": {
       "tags": [
         "/boiler/1/temperature",
         "/boiler/1/pressure",
         "/boiler/2/temperature",
         "/boiler/2/pressure"
       ]
     }
   }
   ```

   The following example `components` object specifies to deploy this industrial dashboard component and configuration update\.

   ```
   {
     "targetArn": "targetArn",
     "components": {
       "com.example.IndustrialDashboard": {
         "componentVersion": "1.0.0",
         "configurationUpdate": {
           "reset": [
             "/network/useHttps",
             "/tags"
           ],
           "merge": "{\"tags\":[\"/boiler/1/temperature\",\"/boiler/1/pressure\",\"/boiler/2/temperature\",\"/boiler/2/pressure\"]}"
         }
       }
     }
   }
   ```

1. \(Optional\) Define deployment policies for the deployment\. You can configure when core devices can safely apply a deployment or what to do if a core device fails to apply the deployment\. To do so, add a `deploymentPolicies` object to `deployment.json`, and then do any of the following:

   1. \(Optional\) Specify the component update policy \(`componentUpdatePolicy`\)\. This policy defines whether or not the deployment lets components defer an update until they are ready to update\. For example, components may need to clean up resources or finish critical actions before they can restart to apply an update\. This policy also defines the amount of time that components have to respond to an update notification\.

      This policy is an object with the following parameters:
      + `action` – \(Optional\) Whether or not to notify components and wait for them to report when they're ready to update\. Choose from the following options:
        + `NOTIFY_COMPONENTS` – The deployment notifies each component before it stops and updates that component\. Components can use the [SubscribeToComponentUpdates](ipc-component-lifecycle.md#ipc-operation-subscribetocomponentupdates) IPC operation to receive these notifications\.
        + `SKIP_NOTIFY_COMPONENTS` – The deployment doesn't notify components or wait for them to be safe to update\.

        Defaults to `NOTIFY_COMPONENTS`\.
      + `timeoutInSeconds` The amount of time in seconds that each component has to respond to an update notification with the [DeferComponentUpdate](ipc-component-lifecycle.md#ipc-operation-defercomponentupdate) IPC operation\. If the component doesn't respond within this amount of time, then the deployment proceeds on the core device\.

        Defaults to 60 seconds\.

   1. \(Optional\) Specify the configuration validation policy \(`configurationValidationPolicy`\)\. This policy defines how long each component has to validate a configuration update from a deployment\. Components can use the [SubscribeToValidateConfigurationUpdates](ipc-component-configuration.md#ipc-operation-subscribetovalidateconfigurationupdates) IPC operation to subscribe to notifications for their own configuration updates\. Then, components can use the [SendConfigurationValidityReport](ipc-component-configuration.md#ipc-operation-sendconfigurationvalidityreport) IPC operation to tell the AWS IoT Greengrass Core software if the configuration update is valid\. If the configuration update isn't valid, the deployment fails\.

      This policy is an object with the following parameter:
      + `timeoutInSeconds` \(Optional\) The amount of time in seconds that each component has to validate a configuration update\. If the component doesn't respond within this amount of time, then the deployment proceeds on the core device\.

        Defaults to 30 seconds\.

   1. \(Optional\) Specify the failure handling policy \(`failureHandlingPolicy`\)\. This policy is a string that defines whether or not to roll back devices if the deployment fails\. Choose from the following options:
      + `ROLLBACK` – If the deployment fails on a core device, then the AWS IoT Greengrass Core software rolls back that core device to its previous configuration\.
      + `DO_NOTHING` – If the deployment fails on a core device, then the AWS IoT Greengrass Core software keeps the new configuration\. This can result in broken components if the new configuration isn't valid\.

      Defaults to `ROLLBACK`\.

   Your deployment in `deployment.json` may look similar to the following example:

   ```
   {
     "targetArn": "targetArn",
     "components": {
       "com.example.IndustrialDashboard": {
         "componentVersion": "1.0.0",
         "configurationUpdate": {
           "reset": [
             "/network/useHttps",
             "/tags"
           ],
           "merge": "{\"tags\":[\"/boiler/1/temperature\",\"/boiler/1/pressure\",\"/boiler/2/temperature\",\"/boiler/2/pressure\"]}"
         }
       }
     },
     "deploymentPolicies": {
       "componentUpdatePolicy": {
         "action": "NOTIFY_COMPONENTS",
         "timeoutInSeconds": 30
       },
       "configurationValidationPolicy": {
         "timeoutInSeconds": 60
       },
       "failureHandlingPolicy": "ROLLBACK"
     }
   }
   ```

1. \(Optional\) Define how the deployment stops, rolls out, or times out\. AWS IoT Greengrass uses AWS IoT Core jobs to send deployments to core devices, so these options are identical to the configuration options for AWS IoT Core jobs\. For more information, see [Job rollout and abort configuration](https://docs.aws.amazon.com/iot/latest/developerguide/job-rollout-abort.html) in the *AWS IoT Developer Guide*\.

   To define the job options, add an `iotJobConfigurations` object to `deployment.json`\. Then, define the options to configure\.

   Your deployment in `deployment.json` may look similar to the following example:

   ```
   {
     "targetArn": "targetArn",
     "components": {
       "com.example.IndustrialDashboard": {
         "componentVersion": "1.0.0",
         "configurationUpdate": {
           "reset": [
             "/network/useHttps",
             "/tags"
           ],
           "merge": "{\"tags\":[\"/boiler/1/temperature\",\"/boiler/1/pressure\",\"/boiler/2/temperature\",\"/boiler/2/pressure\"]}"
         }
       }
     },
     "deploymentPolicies": {
       "componentUpdatePolicy": {
         "action": "NOTIFY_COMPONENTS",
         "timeoutInSeconds": 30
       },
       "configurationValidationPolicy": {
         "timeoutInSeconds": 60
       },
       "failureHandlingPolicy": "ROLLBACK"
     },
     "iotJobConfigurations": {
       "abortConfig": {
         "criteriaList": [
           {
             "action": "CANCEL",
             "failureType": "ALL",
             "minNumberOfExecutedThings": 100,
             "thresholdPercentage": 5
           }
         ]
       },
       "jobExecutionsRolloutConfig": {
         "exponentialRate": {
           "baseRatePerMinute": 5,
           "incrementFactor": 2,
           "rateIncreaseCriteria": {
             "numberOfNotifiedThings": 10,
             "numberOfSucceededThings": 5
           }
         },
         "maximumPerMinute": 50
       },
       "timeoutConfig":  {
         "inProgressTimeoutInMinutes": 5
       }
     }
   }
   ```

1. \(Optional\) Add tags \(`tags`\) for the deployment\. For more information, see [Tag your AWS IoT Greengrass Version 2 resources](tag-resources.md)\.

1. Run the following command to create the deployment from `deployment.json`\.

   ```
   aws greengrassv2 create-deployment \
     --cli-input-json file://deployment.json
   ```

   <a name="check-new-deployment-status"></a>The response includes a `deploymentId` that identifies this deployment\. You can use the deployment ID to check the status of the deployment\. For more information, see [Check deployment status](check-deployment-status.md#check-cloud-deployment-status)\.
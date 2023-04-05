# Create subdeployments<a name="create-subdeployments"></a>

**Note**  
The subdeployment feature is available on Greengrass nucleus version 2\.9\.0 and later\. It is not possible to deploy a configuration to a subdeployment with earlier component versions of Greengrass nucleus\.

A subdeployment is a deployment that targets a smaller subset of devices within a parent deployment\. You can use subdeployments to deploy a configuration to a smaller subset of devices\. You can also create subdeployments to retry an unsuccessful parent deployment when one or more devices in that parent deployment fails\. With this feature, you can select devices that failed in that parent deployment and create a subdeployment to test configurations until the subdeployment is successful\. Once the subdeployment is successful, you can redeploy that configuration to the parent deployment\.

Follow the steps in this section to create a subdeployment and check its status\. For more information about how to create deployments, see [Create deployments](https://docs.aws.amazon.com/greengrass/v2/developerguide/create-deployments.html)\.

**To create a subdeployment \(AWS CLI\)**

1. <a name="create-subdeployments-step1"></a>Run the following command to retrieve the latest deployments for a thing group\. Replace the ARN in the command with the ARN of the thing group to query\. Set `--history-filter` to **LATEST\_ONLY** to see the latest deployment of that thing group\.

   ```
   aws greengrassv2 list-deployments --target-arn arn:aws:iot:region:account-id:thinggroup/thingGroupName --history-filter LATEST_ONLY
   ```

1. Copy the `deploymentId` from the response to the list\-deployments command to use in the next step\.

1. Run the following command to retrieve the status of a deployment\. Replace `deploymentId` with the ID of the deployment to query\.

   ```
   aws greengrassv2 get-deployment --deployment-id deploymentId
   ```

1. Copy the `iotJobId` from the response to the get\-deployment command to use in the following step\.

1. Run the following command to retrieve the list of job executions for the specified job\. Replace *jobID* with the `iotJobId` from the previous step\. Replace *status* with the status you want to filter for\. You can filter results with the following statuses:
   + `QUEUED`
   + `IN_PROGRESS`
   + `SUCCEEDED`
   + `FAILED`
   + `TIMED_OUT`
   + `REJECTED`
   + `REMOVED`
   + `CANCELED`

   ```
   aws iot list-job-executions-for-job --job-id jobID --status status
   ```

1. Create a new AWS IoT thing group, or use an existing thing group, for your subdeployment\. Then, add an AWS IoT thing to this thing group\. You use thing groups to manage fleets of Greengrass core devices\. When you deploy software components to your devices, you can target either individual devices or groups of devices\. You can add a device to a thing group with an active Greengrass deployment\. Once added, you can then deploy that thing group's software components to that device\.

   To create a new thing group and add your devices to it, do the following:

   1. Create an AWS IoT thing group\. Replace *MyGreengrassCoreGroup* with the name for the new thing group\. You can't use a colon \(:\) in a thing group name\.
**Note**  
If a thing group for a subdeployment is used with one `parentTargetArn`, it can't be reused with a different parent fleet\. If a thing group has already been used to create a subdeployment for another fleet, the API will return an error\.

      ```
      aws iot create-thing-group --thing-group-name MyGreengrassCoreGroup
      ```

      If the request succeeds, the response looks similar to the following example:

      ```
      {
        "thingGroupName": "MyGreengrassCoreGroup",
        "thingGroupArn": "arn:aws:iot:us-west-2:123456789012:thinggroup/MyGreengrassCoreGroup",
        "thingGroupId": "4df721e1-ff9f-4f97-92dd-02db4e3f03aa"
      }
      ```

   1. Add a provisioned Greengrass core to your thing group\. Run the following command with these parameters:
      + Replace *MyGreengrassCore* with the name of your provisioned Greengrass core\.
      + Replace *MyGreengrassCoreGroup* with the name of your thing group\.

      ```
      aws iot add-thing-to-thing-group --thing-name MyGreengrassCore --thing-group-name MyGreengrassCoreGroup
      ```

      The command doesn't have any output if the request succeeds\.

1. Create a file called `deployment.json`, and then copy the following JSON object into the file\. Replace *targetArn* with the ARN of the AWS IoT thing group to target for the subdeployment\. A subdeployment target can only be a thing group\. Thing group ARNs have the following format:
   + **Thing group** – `arn:aws:iot:region:account-id:thinggroup/thingGroupName`

   ```
   {
     "targetArn": "targetArn"
   }
   ```

1. Run the following command again to get the original deployment's details\. These details include metadata, components, and job configuration\. Replace *deploymentId* with the ID from [Step 1](#create-subdeployments-step1)\. You can use this deployment configuration to configure your subdeployment and make changes as needed\.

   ```
   aws greengrassv2 get-deployment --deployment-id deploymentId
   ```

   The response contains the deployment's details\. Copy any of the following key\-value pairs from the get\-deployment command's response into `deployment.json`\. You can change these values for the subdeployment\. For more information about the details of this command, see [GetDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_GetDeployment.html)\.
   + `components` – The deployment's components\. To uninstall a component, remove it from this object\.
   + `deploymentName` – The deployment's name\.
   + `deploymentPolicies` – The deployment's policies\.
   + `iotJobConfiguration` – The deployment's job configuration\.
   + `parentTargetArn` – The target of the parent deployment\.
   + `tags` – The deployment's tags\.

1. Run the following command to create the subdeployment from `deployment.json`\. Replace *subdeploymentName* with a name for the subdeployment\.

   ```
   aws greengrassv2 create-deployment --deployment-name subdeploymentName --cli-input-json file://deployment.json
   ```

   The response includes a `deploymentId` that identifies this subdeployment\. You can use the deployment ID to check the status of the deployment\. For more information, see [Check deployment status](https://docs.aws.amazon.com/greengrass/v2/developerguide/check-deployment-status.html#check-cloud-deployment-status)\.

1. If the subdeployment is successful, you can use its configuration to revise the parent deployent\. Copy the `deployment.json` that you used in the previous step\. Replace the `targetArn` in the JSON file with the parent deployment's ARN and run the following command to create the parent deployment using this new configuration\.
**Note**  
If you create a new deployment revision of the parent fleet, it replaces all deployment revisions and subdeployments for that parent deployment\. For more information, see [Revise deployments](https://docs.aws.amazon.com/greengrass/v2/developerguide/revise-deployments.html)\.

   ```
   aws greengrassv2 create-deployment --cli-input-json file://deployment.json
   ```

   <a name="check-new-deployment-status"></a>The response includes a `deploymentId` that identifies this deployment\. You can use the deployment ID to check the status of the deployment\. For more information, see [Check deployment status](check-deployment-status.md#check-cloud-deployment-status)\.
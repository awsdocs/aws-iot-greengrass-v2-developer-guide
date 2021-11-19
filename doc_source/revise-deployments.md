# Revise deployments<a name="revise-deployments"></a>

Each target thing or thing group can have one active deployment at a time\. When you create a deployment for a target that already has a deployment, the software components in the new deployment replace those from the previous deployment\. If the new deployment doesn't define a component that the previous deployment defines, the AWS IoT Greengrass Core software removes that component from the target core devices\. You can revise an existing deployment so that you don't remove the components that run on core devices from a previous deployment to a target\.

To revise a deployment, you create a deployment that starts from the same components and configurations that exist in a previous deployment\. You use the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation, which is the same operation that you use to [create deployments](create-deployments.md)\.

**To revise a deployment \(AWS CLI\)**

1. <a name="revise-deployment-list-deployments-intro"></a>Run the following command to list the deployments for the deployment target\. Replace *targetArn* with the ARN of the target AWS IoT thing or thing group\.

   ```
   aws greengrassv2 list-deployments --target-arn targetArn
   ```

   The response contains a list with the latest deployment for the target\. Copy the `deploymentId` from the response to use in the next step\.
**Note**  <a name="revise-deployment-list-deployments-revision-note"></a>
You can also revise a deployment other than the latest revision for the target\. Specify the `--history-filter ALL` argument to list all deployments for the target\. Then, copy the ID of the deployment that you want to revise\.

1. <a name="revise-deployment-get-deployment"></a>Run the following command to get the deployment's details\. These details include metadata, components, and job configuration\. Replace *deploymentId* with the ID from the previous step\.

   ```
   aws greengrassv2 get-deployment --deployment-id deploymentId
   ```

   The response contains the deployment's details\.

1. Create a file called `deployment.json` and copy the previous command's response into the file\.

1. Remove the following key\-value pairs from the JSON object in `deployment.json`:
   + `deploymentId`
   + `revisionId`
   + `iotJobId`
   + `iotJobArn`
   + `creationTimestamp`
   + `isLatestForTarget`
   + `deploymentStatus`

   The [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation expects a payload with the following structure\.

   ```
   {
     "targetArn": "String",
     "components": Map of components,
     "deploymentPolicies": DeploymentPolicies,
     "iotJobConfiguration": DeploymentIoTJobConfiguration,
     "tags": Map of tags
   }
   ```

1. In `deployment.json`, do any of the following:
   + Change the deployment's name \(`deploymentName`\)\.
   + Change the deployment's components \(`components`\)\.
   + Change the deployment's policies \(`deploymentPolicies`\)\.
   + Change the deployment's job configuration \(`iotJobConfiguration`\)\.
   + Change the deployment's tags \(`tags`\)\.

   For more information about how to define these deployment details, see [Create deployments](create-deployments.md)\.

1. Run the following command to create the deployment from `deployment.json`\.

   ```
   aws greengrassv2 create-deployment --cli-input-json file://deployment.json
   ```

   <a name="check-new-deployment-status"></a>The response includes a `deploymentId` that identifies this deployment\. You can use the deployment ID to check the status of the deployment\. For more information, see [Check deployment status](check-deployment-status.md#check-cloud-deployment-status)\.
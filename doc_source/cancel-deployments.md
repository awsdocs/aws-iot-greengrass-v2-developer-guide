# Cancel deployments<a name="cancel-deployments"></a>

You can cancel an active deployment to prevent its software components from installing on AWS IoT Greengrass core devices\. If you cancel a deployment that targets a thing group, core devices that you add to the group won't receive that continuous deployment\. If a core device already runs the deployment, you won't change the components on that device when you cancel the deployment\. You must [create a new deployment](create-deployments.md) or [revise the deployment](revise-deployments.md) to modify the components that run on the core devices that received the canceled deployment\.

**To cancel a deployment \(AWS CLI\)**

1. Run the following command to find the ID of the latest deployment revision for a target\. The latest revision is the only deployment that can be active for a target, because previous deployments cancel when you create a new revision\. Replace *targetArn* with the ARN of the target AWS IoT thing or thing group\.

   ```
   aws greengrassv2 list-deployments --target-arn targetArn
   ```

   The response contains a list with the latest deployment for the target\. Copy the `deploymentId` from the response to use in the next step\.

1. Run the following command to cancel the deployment\. Replace *deploymentId* with the ID from the previous step\.

   ```
   aws greengrassv2 cancel-deployment --deployment-id deploymentId
   ```

   If the operation succeeds, the deployment status changes to `CANCELED`\.
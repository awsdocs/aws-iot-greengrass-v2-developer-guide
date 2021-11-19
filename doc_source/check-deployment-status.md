# Check deployment status<a name="check-deployment-status"></a>

You can check the status of a deployment that you create in AWS IoT Greengrass\. You can also check the status of the AWS IoT jobs that roll out the deployment to each core device\. While a deployment is active, the AWS IoT job's status is `IN_PROGRESS`\. After you create a new revision of a deployment, the status of the previous revision's AWS IoT job changes to `CANCELLED`\.

**Topics**
+ [Check deployment status](#check-cloud-deployment-status)
+ [Check device deployment status](#check-device-deployment-status)

## Check deployment status<a name="check-cloud-deployment-status"></a>

You can check the status of a deployment that you identify by its target or its ID\.

**To check deployment status by target \(AWS CLI\)**
+ Run the following command to retrieve the status of the latest deployment for a target\. Replace *targetArn* with the ARN of the AWS IoT thing or thing group that the deployment targets\.

  ```
  aws greengrassv2 list-deployments --target-arn targetArn
  ```

  The response contains a list with the latest deployment for the target\. This deployment object includes the status of the deployment\.

**To check deployment status by ID \(AWS CLI\)**
+ Run the following command to retrieve the status of a deployment\. Replace *deploymentId* with the ID of the deployment to query\.

  ```
  aws greengrassv2 get-deployment --deployment-id deploymentId
  ```

  The response contains the status of the deployment\.

## Check device deployment status<a name="check-device-deployment-status"></a>

You can check the status of a deployment job that applies to an individual core device\.

**To check deployment job status \(AWS CLI\)**
+ Run the following command to retrieve the status of all deployment jobs for a core device\. Replace *coreDeviceName* with the name of the core device to query\.

  ```
  aws greengrassv2 list-effective-deployments --core-device-thing-name coreDeviceName
  ```

  The response contains the list of deployment jobs for the core device\. You can identify the job for a deployment by the jobs `deploymentId` or `targetArn`\. Each deployment job contains the status of the job on the core device\.
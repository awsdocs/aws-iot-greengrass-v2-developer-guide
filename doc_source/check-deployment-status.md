# Check deployment status<a name="check-deployment-status"></a>

You can check the status of a deployment that you create in AWS IoT Greengrass\. You can also check the status of the AWS IoT jobs that roll out the deployment to each core device\. While a deployment is active, the AWS IoT job's status is `IN_PROGRESS`\. After you create a new revision of a deployment, the status of the previous revision's AWS IoT job changes to `CANCELLED`\.

**Topics**
+ [Check deployment status](#check-cloud-deployment-status)
+ [Check device deployment status](#check-device-deployment-status)

## Check deployment status<a name="check-cloud-deployment-status"></a>

You can check the status of a deployment that you identify by its target or its ID\.

**To check deployment status by target \(AWS CLI\)**
+ Run the following command to retrieve the status of the latest deployment for a target\. Replace *targetArn* with the Amazon Resource Name \(ARN\) of the AWS IoT thing or thing group that the deployment targets\.

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

You can check the status of a deployment job that applies to an individual core device\. You can also check the status of a deployment job for a thing group deployment\.

**To check deployment job statuses for a core device \(AWS CLI\)**
+ Run the following command to retrieve the status of all deployment jobs for a core device\. Replace *coreDeviceName* with the name of the core device to query\.

  ```
  aws greengrassv2 list-effective-deployments --core-device-thing-name coreDeviceName
  ```

  The response contains the list of deployment jobs for the core device\. You can identify the job for a deployment by the job's `deploymentId` or `targetArn`\. Each deployment job contains the status of the job on the core device\.

**To check deployment statuses for a thing group \(AWS CLI\)**

1. Run the following command to retrieve the ID of an existing deployment\. Replace *targetArn* with the ARN of the target thing group\.

   ```
   aws greengrassv2 list-deployments --target-arn targetArn
   ```

   The response contains a list with the latest deployment for the target\. Copy the `deploymentId` from the response to use in the next step\.
**Note**  
You can also list a deployment other than the latest deployment for the target\. Specify the `--history-filter ALL` argument to list all deployments for the target\. Then, copy the ID of the deployment that you want to check the status of\.

1. Run the following command to get the deployment's details\. Replace *deploymentID* with the ID from the previous step\.

   ```
   aws greengrassv2 get-deployment --deployment-id deploymentId
   ```

   The response contains information about the deployment\. Copy the `iotJobId` from the response to use in the following step\.

1. Run the following command to describe a core device's job execution for the deployment\. Replace *iotJobId* and *coreDeviceThingName* with the job ID from the previous step and the core device you want to check the status for\.

   ```
   aws iot describe-job-execution --job-id iotJobId --thing-name coreDeviceThingName
   ```

   The response contains the status of the core device's deployment job execution and details about the status\. The `detailsMap` contains the following information:
   + `detailed-deployment-status` – The deployment result status, which can be one of the following values:
     + `SUCCESSFUL` – The deployment succeeded\.
     + `FAILED_NO_STATE_CHANGE` – The deployment failed while the core device prepared to apply the deployment\.
     + `FAILED_ROLLBACK_NOT_REQUESTED` – The deployment failed, and the deployment didn't specify to roll back to a previous working configuration, so the core device might not be functioning correctly\.
     + `FAILED_ROLLBACK_COMPLETE` – The deployment failed, and the core device successfully rolled back to a previous working configuration\.
     + `FAILED_UNABLE_TO_ROLLBACK` – The deployment failed, and the core device failed to roll back to a previous working configuration, so the core device might not be functioning correctly\.

     If the deployment failed, check the `deployment-failure-cause` value and the core device's log files to identify the issue\. For more information about how to access the core device's log files, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.
   + `deployment-failure-cause` – An error message that provides additional details about why the job execution failed\.

   The response looks similar to the following example\.

   ```
   {
     "execution": {
       "jobId": "2cc2698a-5175-48bb-adf2-1dd345606ebd",
       "status": "FAILED",
       "statusDetails": {
         "detailsMap": {
           "deployment-failure-cause": "No local or cloud component version satisfies the requirements. Check whether the version constraints conflict and that the component exists in your AWS account with a version that matches the version constraints. If the version constraints conflict, revise deployments to resolve the conflict. Component com.example.HelloWorld version constraints: LOCAL_DEPLOYMENT requires =1.0.0, thinggroup/MyGreengrassCoreGroup requires =1.0.1.",
           "detailed-deployment-status": "FAILED_NO_STATE_CHANGE"
         }
       },
       "thingArn": "arn:aws:iot:us-west-2:123456789012:thing/MyGreengrassCore",
       "queuedAt": "2022-02-15T14:45:53.098000-08:00",
       "startedAt": "2022-02-15T14:46:05.670000-08:00",
       "lastUpdatedAt": "2022-02-15T14:46:20.892000-08:00",
       "executionNumber": 1,
       "versionNumber": 3
     }
   }
   ```
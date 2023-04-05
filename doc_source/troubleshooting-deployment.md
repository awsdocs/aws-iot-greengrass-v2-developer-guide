# Detailed deployment error codes<a name="troubleshooting-deployment"></a>

Use the error codes and solutions in these sections to help resolve issues with component deployment when using the Greengrass nucleus version 2\.8\.0 or later\.

The Greengrass nucleus reports deployment errors as a hierarchy from least specific to the most specific code available\. You can use this hierarchy to help pinpoint the reason for a deployment error\. For example, the following is a possible error hierarchy:
+ DEPLOYMENT\_FAILURE
  + ARTIFACT\_DOWNLOAD\_ERROR
    + IO\_ERROR
      + DISK\_SPACE\_CRITICAL

The error codes are organized into types\. Each type represents a class of errors that can occur\. AWS IoT Greengrass reports these errors types in the console, the API, and AWS CLI\. There can be more than one error type, depending on the errors reported in the error hierarchy\. For the preceding example, the error type returned is `DEVICE_ERROR`\.

The types are:
+ **PERMISSION\_ERROR** – Access to an operation that requires permission was denied\.
+ **REQUEST\_ERROR** – An error occurred due to an issue in the deployment document\.
+ **COMPONENT\_RECIPE\_ERROR** – An error occurred due to an issue in a component recipe\.
+ **AWS\_COMPONENT\_ERROR** – An error occurred when starting or removing an AWS provided component\.
+ **USER\_COMPONENT\_ERROR** – An error occurred when starting or removing a user component\.
+ **COMPONENT\_ERROR** – An error occurred when starting or removing a component, but the Greengrass nucleus couldn't determine if the component is an AWS provided component or a user component\.
+ **DEVICE\_ERROR** – An error occurred with local I/O or another device error occurred\.
+ **DEPENDENCY\_ERROR** – A deployment failed to download an artifact from Amazon S3 or to pull an image from an ECR registry\.
+ **HTTP\_ERROR** – An error occurred with an HTTP request\.
+ **NETWORK\_ERROR** – An error occurred with the device network\.
+ **NUCLEUS\_ERROR** – The Greengrass nucleus could not locate a component or could not find the active nucleus version\.
+ **SERVER\_ERROR** – A server returned a 500 error in response to a request\.
+ **CLOUD\_SERVICE\_ERROR** – An error occurred with the AWS IoT Greengrass cloud service\.
+ **UNKNOWN\_ERROR** – An unchecked exception was thrown by the component\.

Many of the errors in this section report additional information in the AWS IoT Greengrass Core logs\. These logs are stored on the core device's local file system\. There are logs for the AWS IoT Greengrass Core core software and for each individual component\. For information on accessing the logs, see [Access file system logs](monitor-logs.md#access-local-logs)\.

## Permission error<a name="permission-error"></a>

ACCESS\_DENIED  
You might get this error when an AWS service operation returns a 403 error because permissions are not set up correctly\. Check the more specific error code for details\.

GET\_DEPLOYMENT\_CONFIGURATION\_ACCESS\_DENIED  
You might get this error when the AWS IoT policy doesn't allow permission to call the `GetDeploymentConfiguration` operation\. Add the `greengrass::GetDeploymentConfiguration` permission to the core device's policy\.

GET\_COMPONENT\_VERSION\_ARTIFACT\_ACCESS\_DENIED  
You might get this error when the core device AWS IoT policy doesn't allow the `greengrass:GetComponentVersionArtifact` permission\. Add the permission to the core device's policy\.

RESOLVE\_COMPONENT\_CANDIDATES\_ACCESS\_DENIED  
You might get this error when the core device AWS IoT policy doesn't allow the `greengrass:ResolveComponentCandidates` permission\. Add the permission to the core device's policy\.

GET\_ECR\_CREDENTIAL\_ERROR  
You might get this error when the deployment couldn't authenticate with a private registry in ECR\. Check the log for a specific error and then try the deployment again\.

USER\_NOT\_AUTHORIZED\_FOR\_DOCKER  
You might get this error when the Greengrass user is not authorized to use Docker\. Make sure that you are running Greengrass as root or that the user is added to the `docker` group\. Then try the deployment again\.

S3\_ACCESS\_DENIED  
You might get this error when an Amazon S3 operation returns a 403 error\. Check any additional error codes or logs for details\.

S3\_HEAD\_OBJECT\_ACCESS\_DENIED  
You might get this error either when the device's token exchange role doesn't allow the AWS IoT Greengrass Core software to download the component artifact from the S3 object URL that you specify in the component's recipe or that the component artifact isn't available\. Check that the token exchange role allows `s3:GetObject` for the S3 object URL where the artifact is available and that the artifact is present\.

S3\_GET\_BUCKET\_LOCATION\_ACCESS\_DENIED  
You might get this error when the device's token exchange role doesn't allow the `s3:GetBucketLocation` permission for the Amazon S3 bucket where the artifact is available\. Check that the device's allows the permission then try the deployment again\.

S3\_GET\_OBJECT\_ACCESS\_DENIED  
You might get this error either when the device's token exchange role doesn't allow the AWS IoT Greengrass Core software to download the component artifact from the S3 object URL that you specify in the component's recipe or that the component artifact isn't available\. Check that the token exchange role allows `s3:GetObject` for the S3 object URL where the artifact is available and that the artifact is present\.

## Request error<a name="request-error"></a>

NUCLEUS\_MISSING\_REQUIRED\_CAPABILITIES  
You might get this error when the nucleus version in the deployment isn't capable a requested operation, such as downloading a large configuration or setting Linux resource limits\. Retry the deployment with a nucleus version that supports the operation\.

COMPONENT\_CIRCULAR\_DEPENDENCY\_ERROR  
You might get this error when two components in your deployment depend on each other\. Revise the component setup so that the components in your deployment don't rely on each other\. 

UNAUTHORIZED\_NUCLEUS\_MINOR\_VERSION\_UPDATE  
You might get this error when a component in your deployment requires a nucleus minor version update, but that version isn't specified in the deployment\. This helps to reduce accidental minor version updates for components that depend on a different version\. Include the new minor nucleus version in the deployment\.

MISSING\_DOCKER\_APPLICATION\_MANAGER  
You might get this error when you deploy a Docker component without deploying the Docker application manager\. Make sure that your deployment includes the Docker application manager\.

MISSING\_TOKEN\_EXCHANGE\_SERVICE  
You might get this error when the deployment wants to download a Docker image artifact from a private ECR registry without deploying the token exchange service\. Make sure that your deployment includes the token exchange service\.

COMPONENT\_VERSION\_REQUIREMENTS\_NOT\_MET  
You might get this error when there is a version constraint conflict or a component version does not exist\. For more information, see [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.NoAvailableComponentVersionException: Failed to negotiate component <name> version with cloud and no local applicable version satisfying requirement <requirements>](troubleshooting.md#core-error-no-available-component-version)\.

THROTTLING\_ERROR  
You might get this error when an AWS service operation exceeded a rate quota\. Retry the deployment\.

CONFLICTED\_REQUEST  
You might get this error when an AWS service operation returns a 409 error because you deployment is trying to perform more than one operation at a time\. Retry the deployment\.

RESOURCE\_NOT\_FOUND  
You might get this error when an AWS service operation returns a 404 error because a resource couldn't be found\. Check the log for the missing resource\.

RUN\_WITH\_CONFIG\_NOT\_VALID  
You might get this error when the `posixUser`, `posixGroup`, or `windowsUser` information specified to run the component isn't valid\. Check that the user is valid and then retry the deployment\.

UNSUPPORTED\_REGION  
You might get this error when the Region specified for the deployment isn't supported by AWS IoT Greengrass\. Check the Region and try the deployment again\.

IOT\_CRED\_ENDPOINT\_NOT\_VALID  
You might get this error when the AWS IoT credential endpoint specified in the configuration isn't valid\. Check the endpoint and try your request again\.

IOT\_DATA\_ENDPOINT\_NOT\_VALID  
You might get this error when the AWS IoT data endpoint specified in the configuration isn't valid\. Check the endpoint and try your request again\.

S3\_HEAD\_OBJECT\_RESOURCE\_NOT\_FOUND  
You might get this error when the component artifact isn't available at the S3 object URL that you specify in the component's recipe\. Check that you uploaded the artifact to the S3 bucket and that the artifact URI matches the S3 object URL of the artifact in the bucket\.

S3\_GET\_BUCKET\_LOCATION\_RESOURCE\_NOT\_FOUND  
You might get this error when the Amazon S3 bucket isn't found\. Check that the bucket exists and try the deployment again\.

S3\_GET\_OBJECT\_RESOURCE\_NOT\_FOUND  
You might get this error when the component artifact isn't available at the S3 object URL that you specify in the component's recipe\. Check that you uploaded the artifact to the S3 bucket and that the artifact URI matches the S3 object URL of the artifact in the bucket\.

IO\_MAPPING\_ERROR  
You might get this error when an I/O error occurs when parsing deployment document or recipe\. Check any additional error codes or logs for details\.

## Component recipe error<a name="component-recipe-error"></a>

RECIPE\_PARSE\_ERROR  
You might get this error when the deployment recipe couldn't be parsed because there is an error in the structure of the recipe\. Check that the recipe is correctly formatted and try the deployment again\.

RECIPE\_METADATA\_PARSE\_ERROR  
You might get this error when the deployment recipe metadata downloaded from the cloud couldn't be parsed\. Contact AWS Support\.

ARTIFACT\_URI\_NOT\_VALID  
You might get this error when an artifact URI in a recipe isn't formatted correctly\. Check the log for the URI that isn't valid, update the URI in the recipe, then try the deployment again\.

S3\_ARTIFACT\_URI\_NOT\_VALID  
You might get this error when the Amazon S3 URI of an artifact in a recipe isn't valid\. Check the log for the URI that isn't valid, update the URI in the recipe, then try the deployment again\.

DOCKER\_ARTIFACT\_URI\_NOT\_VALID  
You might get this error when the Docker URI of an artifact in a recipe isn't valid\. Check the log for the URI that isn't valid, update the URI in the recipe, then try the deployment again\.

EMPTY\_ARTIFACT\_URI  
You might get this error when the URI of an artifact is not specified in a recipe\. Check the log for the artifact that is missing a URI, update the URI in the recipe, then try the deployment again\.

EMPTY\_ARTIFACT\_SCHEME  
You might get this error when a URI scheme is not defined for an artifact\. Check the log for the URI that isn't valid, update the URI in the recipe, then try the deployment again\.

UNSUPPORTED\_ARTIFACT\_SCHEME  
You might get this error when a URI scheme isn't supported by the running nucleus version\. Either a URI isn't valid or you need to update the nucleus version\. If the URI isn't valid, check the log for the URI that isn't valid, update the URI in the recipe, then try the deployment again\.

RECIPE\_MISSING\_MANIFEST  
You might get this error when the manifest section isn't included in the recipe\. Add the manifest to the recipe and try the deployment again\.

RECIPE\_MISSING\_ARTIFACT\_HASH\_ALGORITHM  
You might get this error when an artifact that is not local is specified inside a recipe without a hash algorithm\. Add the algorithm to the artifact and then try the request again\.

ARTIFACT\_CHECKSUM\_MISMATCH  
You might get this error when a downloaded artifact has a different digest than the one specified in the recipe\. Make sure that the recipe contains the correct digest and then try the deployment again\. For more information see [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.ArtifactChecksumMismatchException: Integrity check for downloaded artifact failed\. Probably due to file corruption\.](troubleshooting.md#core-error-failed-to-download-artifact-checksum-mismatch-exception)\.

COMPONENT\_DEPENDENCY\_NOT\_VALID  
You might get this error when the dependency type specified in a deployment recipe isn't valid\. Check the recipe and then try your request again\.

CONFIG\_INTERPOLATE\_ERROR  
You might get this error when interpolating a recipe variable\. Check the log for details\.

IO\_MAPPING\_ERROR  
You might get this error when an I/O error occurs when parsing deployment document or recipe\. Check any additional error codes or logs for details\.

## AWS component error, user component error, component error<a name="component-error"></a>

The following error codes are returned when there is a problem with a component\. The actual error type reported depends on the specific component that raised the error\. If the Greengrass nucleus identifies the component as one provided by AWS IoT Greengrass, it returns `AWS_COMPONENT_ERROR`\. If the component is identified as a user component, the Greengrass nucleus returns `USER_COMPONENT_ERROR`\. If the Greengrass nucleus can't tell, it returns `COMPONENT_ERROR`\.

COMPONENT\_UPDATE\_ERROR  
You might get this error when a component doesn't update during a deployment\. Check any additional error codes or check the log to see what caused the error\.

COMPONENT\_BROKEN  
You might get this error when a component is broken during a deployment\. Check the component log for error details and then try the deployment again\.

REMOVE\_COMPONENT\_ERROR  
You might get this error when the nucleus can't remove a component during a deployment\. Check the log for error details and then try the deployment again\.

COMPONENT\_BOOTSTRAP\_TIMEOUT  
You might get this error when a component's bootstrap task took longer than the configured timeout\. Increase the timeout or reduce the execution time of the bootstrap task, then try the deployment again\.

COMPONENT\_BOOTSTRAP\_ERROR  
You might get this error when a component's bootstrap task has an error\. Check the log for error details, then try the deployment again\.

COMPONENT\_CONFIGURATION\_NOT\_VALID  
You might get this error when the nucleus can't validate the deployed configuration for the component\. Check the log for error details, then try the deployment again\.

## Device error<a name="device-error"></a>

IO\_WRITE\_ERROR  
You might get this error when writing to a file\. Check the log for details\.

IO\_READ\_ERROR  
You might get this error when reading from a file\. Check the log for details\.

DISK\_SPACE\_CRITICAL  
You might get this error when there is not enough disk space to complete a deployment request\. You must have at least 20 Mb of available space, or enough to hold a larger artifact\. Free up some disk space and then retry the deployment\.

IO\_FILE\_ATTRIBUTE\_ERROR  
You might get this error when the existing file size can't be retrieved from the file system\. Check the log for details\.

SET\_PERMISSION\_ERROR  
You might get this error when the permissions can't be set on a downloaded artifact or artifact directory\. Check the log for details\.

IO\_UNZIP\_ERROR  
You might get this error when an artifact can't be unzipped\. Check the log for details\.

LOCAL\_RECIPE\_NOT\_FOUND  
You might get this error when the local copy of a recipe file couldn't be found\. Try the deployment again\.

LOCAL\_RECIPE\_CORRUPTED  
You might get this error when the local copy of the recipe has changed since it was downloaded\. Delete the existing copy of the recipe and try the deployment again\.

LOCAL\_RECIPE\_METADATA\_NOT\_FOUND  
You might get this error when the local copy of the recipe metadata file couldn't be found\. Try the deployment again\.

LAUNCH\_DIRECTORY\_CORRUPTED  
You might get this error when the directory used to launch the Greengrass nucleus \(`/greengrass/v2/alts/current`\) has been modified since the last time the nucleus was started\. Restart the nucleus and then retry the deployment\.

HASHING\_ALGORITHM\_UNAVAILABLE  
You might get this error when the device's Java distribution doesn't support the required hashing algorithm or when the hash algorithm specified in a component recipe isn't valid\.

DEVICE\_CONFIG\_NOT\_VALID\_FOR\_ARTIFACT\_DOWNLOAD  
You might get this error when there is an error in the device configuration that prevented the deployment from downloading the artifact from Amazon S3 or the Greengrass cloud\. Check the log for a specific configuration error and then retry the deployment\.

## Dependency error<a name="dependency-error"></a>

DOCKER\_ERROR  
You might get this error when pulling a Docker image\. Check any additional error codes or logs for details\.

DOCKER\_SERVICE\_UNAVAILABLE  
You might get this error when Greengrass couldn't log into the Docker registry\. Check the log for a specific error and then try the deployment again\.

DOCKER\_LOGIN\_ERROR  
You might get this error when an unexpected error occurs when logging in to Docker\. Check the log for a specific error and then try the deployment again\.

DOCKER\_PULL\_ERROR  
You might get this error when an unexpected error occurs when pulling a Docker image from the registry\. Check the log for a specific error and then try the deployment again\.

DOCKER\_IMAGE\_NOT\_VALID  
You might get this error when the requested Docker image doesn't exist\. Check the log for a specific error and try the deployment again\.

S3\_ERROR  
You might get this error when downloading an Amazon S3 artifact\. Check any additional error codes or logs for details\.

S3\_RESOURCE\_NOT\_FOUND  
You might get this error when an Amazon S3 operation returns a 404 error\. Check any additional error codes or logs for details\.

S3\_BAD\_REQUEST  
You might get this error when an Amazon S3 operation returns a 400 error\. Check the log for a specific error and try the request again\.

## HTTP error<a name="http-error"></a>

HTTP\_REQUEST\_ERROR  
You might get this error when an error occurred when making an HTTP request\. Check the log for the specific error\.

DOWNLOAD\_DEPLOYMENT\_DOCUMENT\_ERROR  
You might get this error when an HTTP error occurred when downloading the deployment document\. Check the log for the specific HTTP error\.

GET\_GREENGRASS\_ARTIFACT\_SIZE\_ERROR  
You might get this error when an HTTP error occurred when getting the size of a public component artifact\. Check the log for the specific HTTP error\.

DOWNLOAD\_GREENGRASS\_ARTIFACT\_ERROR  
You might get this error when an HTTP error occurred when downloading a public component artifact\. Check the log for the specific HTTP error\.

## Network error<a name="network-error"></a>

NETWORK\_ERROR  
You might get this error when there is a connection issue during a deployment\. Check the connection of the device to the Internet and try the deployment again\.

## Nucleus error<a name="nucleus-error"></a>

BAD\_REQUEST  
You might get this error when an AWS cloud operation returns a 400 error\. Check the log to see which API caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

NUCLEUS\_VERSION\_NOT\_FOUND  
You might get this error when a core device can't find the version of the active nucleus\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

NUCLEUS\_RESTART\_FAILURE  
You might get this error when the nucleus doesn't restart during any deployment that requires a nucleus restart\. Check the loader log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\. 

INSTALLED\_COMPONENT\_NOT\_FOUND  
You might get this error when the nucleus can't locate an installed component\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

DEPLOYMENT\_DOCUMENT\_NOT\_VALID  
You might get this error when the device receives deployment document that isn't valid\. Check any additional error codes or check the log to see what caused the error\.

EMPTY\_DEPLOYMENT\_REQUEST  
You might get this error when a device receives an empty deployment request\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

DEPLOYMENT\_DOCUMENT\_PARSE\_ERROR  
You might get this error when the deployment request format doesn't match the expected format\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

COMPONENT\_METADATA\_NOT\_VALID\_IN\_DEPLOYMENT  
You might get this error when the deployment request contains component metadata that isn't valid\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

## Server error<a name="server-error"></a>

SERVER\_ERROR  
You might get this error when an AWS service operation returns a 500 error because the service can't process the request right now\. Retry the deployment later\.

S3\_SERVER\_ERROR  
You might get this error when an Amazon S3 operation returns a 500 error\. Check any additional error codes or logs for details\.

## Cloud service error<a name="cloud-service-error"></a>

RESOLVE\_COMPONENT\_CANDIDATES\_BAD\_RESPONSE  
You might get this error when the Greengrass cloud service sends an incompatible response to the `ResolveComponentCandidates` operation\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

MULTIPLE\_NUCLEUS\_RESOLVED\_ERROR  
You might get this error when a deployment attempts to deploy multiple nucleus components\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

DEPLOYMENT\_DOCUMENT\_SIZE\_EXCEEDED  
You might get this error when the requested deployment document exceeded the maximum size quota\. Reduce the size of the deployment document and try the deployment again\.

GREENGRASS\_ARTIFACT\_SIZE\_NOT\_FOUND  
You might get this error when Greengrass can't get the size of a public component artifact\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

DEPLOYMENT\_DOCUMENT\_NOT\_VALID  
You might get this error when the device receives deployment document that isn't valid\. Check any additional error codes or check the log to see what caused the error\.

EMPTY\_DEPLOYMENT\_REQUEST  
You might get this error when a device receives an empty deployment request\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

DEPLOYMENT\_DOCUMENT\_PARSE\_ERROR  
You might get this error when the deployment request format doesn't match the expected format\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

COMPONENT\_METADATA\_NOT\_VALID\_IN\_DEPLOYMENT  
You might get this error when the deployment request contains component metadata that isn't valid\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

## Generic errors<a name="generic-error"></a>

These generic errors do not have an associated error type\.

DEPLOYMENT\_INTERRUPTED  
You might get this error when a deployment can't be completed because of a nucleus shutdown or other external event\. Check any additional error codes or logs for details\.

ARTIFACT\_DOWNLOAD\_ERROR  
You might get this error when there is a problem downloading an artifact\. Check any additional error codes or logs for details\.

NO\_AVAILABLE\_COMPONENT\_VERSION  
You might get this error when a component version doesn't exist in the cloud or locally, or if there is a dependency resolution conflict\. Check any additional error codes or logs for details\.

COMPONENT\_PACKAGE\_LOADING\_ERROR  
You might get this error when an error processing the downloaded artifacts\. Check any additional error codes or logs for details\.

CLOUD\_API\_ERROR  
You might get this error when an error occurred calling an AWS service API\. Check any additional error codes or logs for details\.

IO\_ERROR  
You might get this error when an I/O error occurs during a deployment\. Check any additional error codes or logs for details\.

COMPONENT\_UPDATE\_ERROR  
You might get this error when a component doesn't update during a deployment\. Check any additional error codes or check the log to see what caused the error\.

## Unknown error<a name="unknown-error"></a>

DEPLOYMENT\_FAILURE  
You might get this error when a deployment fails because an unchecked exception was thrown\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.

DEPLOYMENT\_TYPE\_NOT\_VALID  
You might get this error when the deployment type isn't valid\. Check the log to see what caused the error, then check the nucleus software update page to see if the issue has been corrected in a later version of the nucleus, or contact AWS Support\.
# Manage local deployments and components<a name="ipc-local-deployments-components"></a>

**Note**  
This feature is available for v2\.6\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

Use the Greengrass CLI IPC service to manage local deployments and Greengrass components on the core device\.

To use these IPC operations, include version 2\.6\.0 or later of the [Greengrass CLI component](greengrass-cli-component.md) as a dependency in your custom component\. You can then use IPC operations in your custom components to do the following:
+ Create local deployments to modify and configure Greengrass components on the core device\.
+ Restart and stop Greengrass components on the core device\.
+ Generate a password that you can use to sign in to the [local debug console](local-debug-console-component.md)\.

**Topics**
+ [Minimum SDK versions](#ipc-local-deployments-components-sdk-versions)
+ [Authorization](#ipc-local-deployments-components-authorization)
+ [CreateLocalDeployment](#ipc-operation-createlocaldeployment)
+ [ListLocalDeployments](#ipc-operation-listlocaldeployments)
+ [GetLocalDeploymentStatus](#ipc-operation-getlocaldeploymentstatus)
+ [ListComponents](#ipc-operation-listcomponents)
+ [GetComponentDetails](#ipc-operation-getcomponentdetails)
+ [RestartComponent](#ipc-operation-restartcomponent)
+ [StopComponent](#ipc-operation-stopcomponent)
+ [CreateDebugPassword](#ipc-operation-createdebugpassword)

## Minimum SDK versions<a name="ipc-local-deployments-components-sdk-versions"></a>

The following table lists the minimum versions of the AWS IoT Device SDK that you must use to interact with the Greengrass CLI IPC service\.


| SDK | Minimum version | 
| --- | --- | 
|  [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2)  |  v1\.2\.10  | 
|  [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2)  |  v1\.5\.3  | 
|  [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2)  |  v1\.17\.0  | 

## Authorization<a name="ipc-local-deployments-components-authorization"></a>

To use the Greengrass CLI IPC service in a custom component, you must define authorization policies that allow your component to manage local deployments and components\. For information about defining authorization policies, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies)\.

Authorization policies for the Greengrass CLI have the following properties\.

**IPC service identifier:** `aws.greengrass.Cli`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#CreateLocalDeployment`  |  Allows a component to create a local deployment on the core device\.  |  `*`  | 
|  `aws.greengrass#ListLocalDeployments`  |  Allows a component to list local deployments on the core device\.  |  `*`  | 
|  `aws.greengrass#GetLocalDeploymentStatus`  |  Allows a component to get the status of a local deployment on the core device\.  |  A local deployment ID, or `*` to allow access to all local deployments\.  | 
|  `aws.greengrass#ListComponents`  |  Allows a component to list components on the core device\.  |  `*`  | 
|  `aws.greengrass#GetComponentDetails`  |  Allows a component to get details about a component on the core device\.  |  A component name, such as `com.example.HelloWorld`, or `*` to allow access to all components\.  | 
|  `aws.greengrass#RestartComponent`  |  Allows a component to restart a component on the core device\.  |  A component name, such as `com.example.HelloWorld`, or `*` to allow access to all components\.  | 
|  `aws.greengrass#StopComponent`  |  Allows a component to stop a component on the core device\.  |  A component name, such as `com.example.HelloWorld`, or `*` to allow access to all components\.  | 
|  `aws.greengrass#CreateDebugPassword`  |  Allows a component to generate a password to use to sign in to the [local debug console component](local-debug-console-component.md)\.  |  `*`  | 

**Example authorization policy**  
The following example authorization policies allow a component to create local deployments, view all local deployments and components, and restart and stop a component named `com.example.HelloWorld`\.  

```
{
  "accessControl": {
    "aws.greengrass.Cli": {
      "com.example.MyLocalManagerComponent:cli:1": {
        "policyDescription": "Allows access to create local deployments and view deployments and components.",
        "operations": [
          "aws.greengrass#CreateLocalDeployment",
          "aws.greengrass#ListLocalDeployments",
          "aws.greengrass#GetLocalDeploymentStatus",
          "aws.greengrass#ListComponents",
          "aws.greengrass#GetComponentDetails"
        ],
        "resources": [
          "*"
        ]
      }
    },
    "aws.greengrass.Cli": {
      "com.example.MyLocalManagerComponent:cli:2": {
        "policyDescription": "Allows access to restart and stop the Hello World component.",
        "operations": [
          "aws.greengrass#RestartComponent",
          "aws.greengrass#StopComponent"
        ],
        "resources": [
          "com.example.HelloWorld"
        ]
      }
    }
  }
}
```

## CreateLocalDeployment<a name="ipc-operation-createlocaldeployment"></a>

Create or update a local deployment using specified component recipes, artifacts, and runtime arguments\.

This operation provides the same functionality as the [deployment create command](gg-cli-deployment.md#deployment-create) in the Greengrass CLI\.

### Request<a name="ipc-operation-createlocaldeployment-request"></a>

This operation's request has the following parameters:

`recipeDirectoryPath` \(Python: `recipe_directory_path`\)  
\(Optional\) The absolute path to the folder that contains component recipe files\.

`artifactDirectoryPath` \(Python: `artifact_directory_path`\)  
\(Optional\) The absolute path to the folder that contains the artifact files to include in the deployment\. The artifacts folder must contain the following folder structure:  

```
/path/to/artifact/folder/component-name/component-version/artifacts
```

`rootComponentVersionsToAdd` \(Python: `root_component_versions_to_add`\)  
\(Optional\) The component versions to install on the core device\. This object, `ComponentToVersionMap`, is a map that contains the following key\-value pairs:    
`key`  
The name of the component\.  
`value`  
The version of the component\.

`rootComponentsToRemove` \(Python: `root_components_to_remove`\)  
\(Optional\) The components to uninstall from the core device\. Specify a list where each entry is the name of a component\.

`componentToConfiguration` \(Python: `component_to_configuration`\)  
\(Optional\) The configuration updates for each component in the deployment\. This object, `ComponentToConfiguration`, is a map that contains the following key\-value pairs:    
`key`  
The name of the component\.  
`value`  
The configuration update JSON object for the component\. The JSON object must have the following format\.  

```
{ 
  "MERGE": {
    "config-key": "config-value"
  },
  "RESET": [
    "path/to/reset/"
  ]
}
```
For more information about configuration updates, see [Update component configurations](update-component-configurations.md)\.

`componentToRunWithInfo` \(Python: `component_to_run_with_info`\)  
\(Optional\) The runtime configuration for each component in the deployment\. This configuration includes the system user that owns each component's processes and the system limits to apply to each component\. This object, `ComponentToRunWithInfo`, is a map that contains the following key\-value pairs:    
`key`  
The name of the component\.  
`value`  
The runtime configuration for the component\. If you omit a runtime configuration parameter, the AWS IoT Greengrass Core software uses the default values that you configure on the [Greengrass nucleus](greengrass-nucleus-component.md)\. This object, `RunWithInfo`, contains the following information:    
`posixUser` \(Python: `posix_user`\)  
\(Optional\) <a name="deployment-posix-user-definition"></a>The POSIX system user and, optionally, group to use to run this component on Linux core devices\. The user, and group if specified, must exist on each Linux core device\. Specify the user and group separated by a colon \(`:`\) in the following format: `user:group`\. The group is optional\. If you don't specify a group, the AWS IoT Greengrass Core software uses the primary group for the user\. For more information, see [Configure the user that runs components](configure-greengrass-core-v2.md#configure-component-user)\.  
`windowsUser` \(Python: `windows_user`\)  
\(Optional\) <a name="deployment-windows-user-definition"></a>The Windows user to use to run this component on Windows core devices\. The user must exist on each Windows core device, and its name and password must be stored in the LocalSystem account's Credentials Manager instance\. For more information, see [Configure the user that runs components](configure-greengrass-core-v2.md#configure-component-user)\.  
`systemResourceLimits` \(Python: `system_resource_limits`\)  
\(Optional\) <a name="deployment-system-resource-limits-definition"></a>The system resource limits to apply to this component's processes\. You can apply system resource limits to generic and non\-containerized Lambda components\. For more information, see [Configure system resource limits for components](configure-greengrass-core-v2.md#configure-component-system-resource-limits)\.  
AWS IoT Greengrass doesn't currently support this feature on Windows core devices\.   
This object, `SystemResourceLimits`, contains the following information:    
`cpus`  
\(Optional\) <a name="system-resource-limits-cpu-definition-this"></a>The maximum amount of CPU time that this component's processes can use on the core device\. A core device's total CPU time is equivalent to the device's number of CPU cores\. For example, on a core device with 4 CPU cores, you can set this value to `2` to limit this component's processes to 50 percent usage of each CPU core\. On a device with 1 CPU core, you can set this value to `0.25` to limit this component's processes to 25 percent usage of the CPU\. If you set this value to a number greater than the number of CPU cores, the AWS IoT Greengrass Core software doesn't limit the component's CPU usage\.  
`memory`  
\(Optional\) <a name="system-resource-limits-memory-definition-this"></a>The maximum amount of RAM \(in kilobytes\) that this component's processes can use on the core device\.

`groupName` \(Python: `group_name`\)  
\(Optional\) The name of the thing group to target with this deployment\.

### Response<a name="ipc-operation-createlocaldeployment-response"></a>

This operation's response has the following information:

`deploymentId` \(Python: `deployment_id`\)  
The ID of the local deployment that the request created\.

## ListLocalDeployments<a name="ipc-operation-listlocaldeployments"></a>

Gets the status of the last 10 local deployments\.

This operation provides the same functionality as the [deployment list command](gg-cli-deployment.md#deployment-list) in the Greengrass CLI\.

### Request<a name="ipc-operation-listlocaldeployments-request"></a>

This operation's request doesn't have any parameters\.

### Response<a name="ipc-operation-listlocaldeployments-response"></a>

This operation's response has the following information:

`localDeployments` \(Python: `local_deployments`\)  
The list of local deployments\. Each object in this list is a `LocalDeployment` object, which contains the following information:  <a name="ipc-local-deployment-object-shape"></a>  
`deploymentId` \(Python: `deployment_id`\)  
The ID of the local deployment\.  
`status`  
The status of the local deployment\. This enum, `DeploymentStatus`, has the following values:  
+ `QUEUED`
+ `IN_PROGRESS`
+ `SUCCEEDED`
+ `FAILED`

## GetLocalDeploymentStatus<a name="ipc-operation-getlocaldeploymentstatus"></a>

Gets the status of a local deployment\.

This operation provides the same functionality as the [deployment status command](gg-cli-deployment.md#deployment-status) in the Greengrass CLI\.

### Request<a name="ipc-operation-getlocaldeploymentstatus-request"></a>

This operation's request has the following parameters:

`deploymentId` \(Python: `deployment_id`\)  
The ID of the local deployment to get\.

### Response<a name="ipc-operation-getlocaldeploymentstatus-response"></a>

This operation's response has the following information:

`deployment`  
The local deployment\. This object, `LocalDeployment`, contains the following information:  <a name="ipc-local-deployment-object-shape"></a>  
`deploymentId` \(Python: `deployment_id`\)  
The ID of the local deployment\.  
`status`  
The status of the local deployment\. This enum, `DeploymentStatus`, has the following values:  
+ `QUEUED`
+ `IN_PROGRESS`
+ `SUCCEEDED`
+ `FAILED`

## ListComponents<a name="ipc-operation-listcomponents"></a>

Gets the name, version, status, and configuration of each root component on the core device\. A *root component* is a component that you specify in a deployment\. This response doesn't include components that are installed as dependencies of other components\.

This operation provides the same functionality as the [component list command](gg-cli-component.md#component-list) in the Greengrass CLI\.

### Request<a name="ipc-operation-listcomponents-request"></a>

This operation's request doesn't have any parameters\.

### Response<a name="ipc-operation-listcomponents-response"></a>

This operation's response has the following information:

`components`  
The list of root components on the core device\. Each object in this list is a `ComponentDetails` object, which contains the following information:  <a name="ipc-component-details-object-shape"></a>  
`componentName` \(Python: `component_name`\)  
The name of the component\.  
`version`  
The version of the component\.  
`state`  
The state of the component\. This state can be one of the following:  
+ `RUNNING`
+ `ERRORED`  
`configuration`  
The component's configuration as a JSON object\.

## GetComponentDetails<a name="ipc-operation-getcomponentdetails"></a>

Gets the version, status, and configuration of a component on the core device\.

This operation provides the same functionality as the [component details command](gg-cli-component.md#component-details) in the Greengrass CLI\.

### Request<a name="ipc-operation-getcomponentdetails-request"></a>

This operation's request has the following parameters:

`componentName` \(Python: `component_name`\)  
The name of the component to get\.

### Response<a name="ipc-operation-getcomponentdetails-response"></a>

This operation's response has the following information:

`componentDetails` \(Python: `component_details`\)  
The component's details\. This object, `ComponentDetails`, contains the following information:  <a name="ipc-component-details-object-shape"></a>  
`componentName` \(Python: `component_name`\)  
The name of the component\.  
`version`  
The version of the component\.  
`state`  
The state of the component\. This state can be one of the following:  
+ `RUNNING`
+ `ERRORED`  
`configuration`  
The component's configuration as a JSON object\.

## RestartComponent<a name="ipc-operation-restartcomponent"></a>

Restarts a component on the core device\.

**Note**  
While you can restart any component, we recommend that you restart only [generic components](develop-greengrass-components.md#component-types)\.

This operation provides the same functionality as the [component restart command](gg-cli-component.md#component-restart) in the Greengrass CLI\.

### Request<a name="ipc-operation-restartcomponent-request"></a>

This operation's request has the following parameters:

`componentName` \(Python: `component_name`\)  
The name of the component\.

### Response<a name="ipc-operation-restartcomponent-response"></a>

This operation's response has the following information:

`restartStatus` \(Python: `restart_status`\)  
The status of the restart request\. The request status can be one of the following:  
+ `SUCCEEDED`
+ `FAILED`

`message`  
A message about why the component failed to restart, if the request failed\.

## StopComponent<a name="ipc-operation-stopcomponent"></a>

Stops a component's processes on the core device\.

**Note**  
While you can stop any component, we recommend that you stop only [generic components](develop-greengrass-components.md#component-types)\.

This operation provides the same functionality as the [component stop command](gg-cli-component.md#component-stop) in the Greengrass CLI\.

### Request<a name="ipc-operation-stopcomponent-request"></a>

This operation's request has the following parameters:

`componentName` \(Python: `component_name`\)  
The name of the component\.

### Response<a name="ipc-operation-stopcomponent-response"></a>

This operation's response has the following information:

`stopStatus` \(Python: `stop_status`\)  
The status of the stop request\. The request status can be one of the following:  
+ `SUCCEEDED`
+ `FAILED`

`message`  
A message about why the component failed to stop, if the request failed\.

## CreateDebugPassword<a name="ipc-operation-createdebugpassword"></a>

Generates a random password that you can use to sign in to the [local debug console component](local-debug-console-component.md)\. The password expires 8 hours after it is generated\.

This operation provides the same functionality as the [get\-debug\-password command](gg-cli-get-debug-password.md) in the Greengrass CLI\.

### Request<a name="ipc-operation-createdebugpassword-request"></a>

This operation's request doesn't have any parameters\.

### Response<a name="ipc-operation-createdebugpassword-response"></a>

This operation's response has the following information:

`username`  
The user name to use to sign in\.

`password`  
The password to use to sign in\.

`passwordExpiration` \(Python: `password_expiration`\)  
The time when the password expires\.

`certificateSHA256Hash` \(Python: `certificate_sha256_hash`\)  
The SHA\-256 fingerprint for the self\-signed certificate that the local debug console uses when HTTPS is enabled\. When you open the local debug console, use this fingerprint to verify that the certificate is legitimate and the connection is secure\.

`certificateSHA1Hash` \(Python: `certificate_sha1_hash`\)  
The SHA\-1 fingerprint for the self\-signed certificate that the local debug console uses when HTTPS is enabled\. When you open the local debug console, use this fingerprint to verify that the certificate is legitimate and the connection is secure\.
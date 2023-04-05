# component<a name="gg-cli-component"></a>

Use the `component` command to interact with local components on your core device\. 

**Subcommands**
+ [details](#component-details)
+ [list](#component-list)
+ [restart](#component-restart)
+ [stop](#component-stop)

## details<a name="component-details"></a>

Retrieve the version, status, and configuration of one component\. 

**Synopsis**  

```
greengrass-cli component details --name <component-name> 
```

**Arguments**  
`--name`, `-n`\. The component name\.

**Output**  
The following example shows the output produced when you run this command\.  

```
$ sudo greengrass-cli component details --name MyComponent 

Component Name: MyComponent 
Version: 1.0.0
State: RUNNING
Configuration: null
```

## list<a name="component-list"></a>

Retrieve the name, version, status, and configuration of each component installed on the device\.

**Synopsis**  

```
greengrass-cli component list
```

**Arguments**  
None

**Output**  
The following example shows the output produced when you run this command\.  

```
$ sudo greengrass-cli component list

Components currently running in Greengrass:
Component Name: FleetStatusService
Version: 0.0.0
State: RUNNING
Configuration: {"periodicUpdateIntervalSec":86400.0}
Component Name: UpdateSystemPolicyService
Version: 0.0.0
State: RUNNING
Configuration: null
Component Name: aws.greengrass.Nucleus
Version: 2.0.0
State: FINISHED
Configuration: {"awsRegion":"region","runWithDefault":{"posixUser":"ggc_user:ggc_group"},"telemetry":{}}
Component Name: DeploymentService
Version: 0.0.0
State: RUNNING
Configuration: null
Component Name: TelemetryAgent
Version: 0.0.0
State: RUNNING
Configuration: null
Component Name: aws.greengrass.Cli
Version: 2.0.0
State: RUNNING
Configuration: {"AuthorizedPosixGroups":"ggc_user"}
```

## restart<a name="component-restart"></a>

Restart components\.

**Synopsis**  

```
greengrass-cli component restart --names <component-name>,...
```

**Arguments**  
`--names`, `-n`\. The component name\. At least one component name is required\. You can specify additional component names, separating each name with a comma\.

**Output**  
None

## stop<a name="component-stop"></a>

Stop running components\. 

**Synopsis**  

```
greengrass-cli component stop --names <component-name>,...
```

**Arguments**  
`--names`, `-n`\. The component name\. At least one component name is required\. You can specify additional component names if needed, separating each name with a comma\.

**Output**  
None
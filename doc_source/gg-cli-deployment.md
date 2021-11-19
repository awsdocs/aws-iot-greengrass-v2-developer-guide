# deployment<a name="gg-cli-deployment"></a>

Use the `deployment` command to interact with local components on your core device\. 

**Subcommands**
+ [create](#deployment-create)
+ [list](#deployment-list)
+ [status](#deployment-status)

## create<a name="deployment-create"></a>

Create or update a local deployment using specified component recipes, artifacts, and runtime arguments\.

**Synopsis**  

```
greengrass-cli deployment create 
    --recipeDir path/to/component/recipe
    [--artifactDir path/to/artifact/folder ]
    [--update-config {component-configuration}]
    [--groupId <thing-group>]
    [--merge "<component-name>=<component-version>"]...
    [--runWith "<component-name>:posixUser=<user-name>[:<group-name>]"]...
    [--systemLimits "{component-system-resource-limits}]"]...
    [--remove <component-name>,...]
```

**Arguments**  
+ `--recipeDir`, `-r`\. The full path to the folder that contains the component recipe files\.
+ `--artifactDir`, `-a`\. The full path to the folder that contains the artifact files you want to include in your deployment\. The artifacts folder must contain the following directory structure:

  ```
  /path/to/artifact/folder/<artifact-name>/<version>/<artifacts>
  ```
+ `--update-config`, `-c`\. The configuration arguments for the deployment, provided as a JSON string or a JSON file\. The JSON string should be in the following format: 

  ```
  { \
    "componentName": { \ 
      "MERGE": {"config-key": "config-value"}, \
      "RESET": ["path/to/reset/"] \
    } \
  }
  ```

  `MERGE` and `RESET` are case\-sensitive and must be in upper case\.
+ `--groupId`, `-g`\. The target thing group for the deployment\.
+ `--merge`, `-m`\. The name and version of the target component that you want to add or update\. You must provide the component information in the format `<component>=<version>`\. Use a separate argument for each additional component to specify\. If needed, use the `--runWith` argument to provide the `posixUser`, `posixGroup`, and `windowsUser` information for running the component\.
+ `--runWith`\. The `posixUser`, `posixGroup`, and `windowsUser` information for running a generic or Lambda component\. You must provide this information in the format `<component>:{posixUser|windowsUser}=<user>[:<=posixGroup>]`\. For example, you might specify **HelloWorld:posixUser=ggc\_user:ggc\_group** or **HelloWorld:windowsUser=ggc\_user**\. Use a separate argument for each additional option to specify\.

  For more information, see [Configure the user that runs components](configure-greengrass-core-v2.md#configure-component-user)\.
+ `--systemLimits`\. The system resource limits to apply to generic and non\-containerized Lambda components' processes on the core device\. You can configure the maximum amount of CPU and RAM usage that each component's processes can use on the core device\. Specify a serialized JSON object or a file path to a JSON file\. The JSON object must have the following format\.

  ```
  {  \
    "componentName": { \ 
      "cpus": cpuTimeLimit, \
      "memory": memoryLimitInKb \
    } \
  }
  ```

  You can configure the following system resource limits for each component:
  + `cpus` – <a name="system-resource-limits-cpu-definition-this"></a>The maximum amount of CPU time that this component's processes can use on the core device\. A core device's total CPU time is equivalent to the device's number of CPU cores\. For example, on a core device with 4 CPU cores, you can set this value to `2` to limit this component's processes to 50 percent usage of each CPU core\. On a device with 1 CPU core, you can set this value to `0.25` to limit this component's processes to 25 percent usage of the CPU\. If you set this value to a number greater than the number of CPU cores, the AWS IoT Greengrass Core software doesn't limit the component's CPU usage\.
  + `memory` – <a name="system-resource-limits-memory-definition-this"></a>The maximum amount of RAM \(in kilobytes\) that this component's processes can use on the core device\.

  For more information, see [Configure system resource limits for components](configure-greengrass-core-v2.md#configure-component-system-resource-limits)\.

  This feature is available for v2\.4\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md) and Greengrass CLI on Linux core devices\. AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 
+ `--remove`\. The name of the target component that you want to remove from a local deployment\. To remove a component that was merged from a cloud deployment, you must provide the group ID of the target thing group in the following format:

------
#### [ Greengrass nucleus v2\.4\.0 and later ]

  ```
  --remove <component-name> --groupId <group-name>
  ```

------
#### [ Earlier than v2\.4\.0 ]

  ```
  --remove <component-name> --groupId thinggroup/<group-name>
  ```

------

**Output**  
The following example shows the output produced when you run this command\.  

```
$ sudo greengrass-cli deployment create \
    --merge MyApp1=1.0.0 \
    --merge MyApp2=1.0.0 --runWith MyApp2:posixUser=ggc_user \
    --remove MyApp3 \
    --recipeDir recipes/ \ 
    --artifactDir artifacts/

Local deployment has been submitted! Deployment Id: 44d89f46-1a29-4044-ad89-5151213dfcbc
```

## list<a name="deployment-list"></a>

Retrieve the status of the last 10 local deployments\.

**Synopsis**  

```
greengrass-cli deployment list
```

**Arguments**  
None

**Output**  
The following example shows the output produced when you run this command\. Depending on the status of your deployment, the output shows one of the following status values: `IN_PROGRESS`, `SUCCEEDED`, or `FAILED`\.  

```
$ sudo greengrass-cli deployment list

44d89f46-1a29-4044-ad89-5151213dfcbc: SUCCEEDED
```

## status<a name="deployment-status"></a>

Retrieve the status of a specific deployment\.

**Synopsis**  

```
greengrass-cli deployment status -i <deployment-id>
```

**Arguments**  
`-i`\. The ID of the deployment\.

**Output**  
The following example shows the output produced when you run this command\. Depending on the status of your deployment, the output shows one of the following status values: `IN_PROGRESS`, `SUCCEEDED`, or `FAILED`\.  

```
$ sudo greengrass-cli deployment status -i 44d89f46-1a29-4044-ad89-5151213dfcbc

44d89f46-1a29-4044-ad89-5151213dfcbc: FAILED
```
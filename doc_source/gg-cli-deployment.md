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
$ greengrass-cli deployment create 
    [--artifactDir path/to/artifact/folder ]
    [--update-config {component-configuration}]
    [--groupId <thing-group>]
    [--merge "<component-name>=<component-version>"]...
    [--runWith "<component-name>:posixUser=<user-name>[:<group-name>]"]...
    --recipeDir path/to/component/recipe
    [--remove <component-name>,...]
```

**Arguments**  
+ `--artifactDir`, `-a`\. The full path to the folder that contains the artifact files you want to include in your deployment\. The artifacts folder must contain the following directory structure:

  ```
  /path/to/artifact/folder/<artifact-name>/<version>/<artifacts>
  ```
+ `--update-config`, `-c`\. The configuration arguments for the deployment, provided as a JSON string or a JSON file\. The JSON string should be in the following format: 

  ```
  {"componentName": { \ 
    "MERGE": {"config-key": "config-value"}, \
    "RESET": ["path/to/reset/"] \
    } \
  }
  ```

  `MERGE` and `RESET` are case\-sensitive and must be in upper case\.
+ `--groupId`, `-g`\. The target thing group for the deployment\.
+ `--merge`, `-m`\. The name and version of the target component that you want to add or update\. You must provide the component information in the format `<component>=<version>`\. Use a separate argument for each additional component to specify\. If needed, use the `--runWith` argument to provide the `posixUser` and `posixGroup` information for running the component\.
+ `--runWith`\. The `posixUser` and `posixGroup` information for running a component\. You must provide this information in the format `<component>:posixUser=<user>[:<group>]`\. For example, `HelloWorld:posixUser=ggc_user:ggc_group`\. Use a separate argument for each additional option to specify\.
+ `--recipeDir`, `-r`\. The full path to the folder that contains the component recipe files\.
+ `--remove`\. The name of the target component that you want to remove from a local deployment\. To remove a component that was merged from a cloud deployment, you must provide the group ID of the target thing group in the following format:

  ```
  --remove <component-name> --groupId thinggroup/<group-name>
  ```

**Output**  
The following example shows the output produced when you run this command\.  

```
$ greengrass-cli deployment create \
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
$ greengrass-cli deployment list
```

**Arguments**  
None

**Output**  
The following example shows the output produced when you run this command\. Depending on the status of your deployment, the output shows one of the following status values: `IN_PROGRESS`, `SUCCEEDED`, or `FAILED`\.  

```
$ greengrass-cli deployment list

44d89f46-1a29-4044-ad89-5151213dfcbc: SUCCEEDED
```

## status<a name="deployment-status"></a>

Retrieve the status of a specific deployment\.

**Synopsis**  

```
$ greengrass-cli deployment status -i <deployment-id>
```

**Arguments**  
`-i`\. The ID of the deployment\.

**Output**  
The following example shows the output produced when you run this command\. Depending on the status of your deployment, the output shows one of the following status values: `IN_PROGRESS`, `SUCCEEDED`, or `FAILED`\.  

```
$ greengrass-cli deployment status -i 44d89f46-1a29-4044-ad89-5151213dfcbc

44d89f46-1a29-4044-ad89-5151213dfcbc: FAILED
```
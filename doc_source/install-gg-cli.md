# Install the Greengrass CLI<a name="install-gg-cli"></a>

You can install the Greengrass CLI in one of the following ways: 
+ Use the `--deploy-dev-tools` argument when you first set up AWS IoT Greengrass Core software on your device\. You must also specify `--provision true` to apply this argument\.
+ Deploy the Greengrass CLI component \(`aws.greengrass.Cli`\) on your device\.

This section describes the steps to deploy the Greengrass CLI component\. For information about installing the Greengrass CLI during initial setup, see [Getting started with AWS IoT Greengrass V2](getting-started.md)\.

## Prerequisites<a name="gg-cli-prereqs"></a>

You must meet the following requirements to deploy the Greengrass CLI component:
+ AWS IoT Greengrass Core software installed and configured on your core device\. For more information, see [Getting started with AWS IoT Greengrass V2](getting-started.md)\. 
+ To use the AWS CLI to deploy the Greengrass CLI, you must have installed and configured the AWS CLI on your Linux device\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS Command Line Interface User Guide*\.

After you deploy the Greengrass CLI component, you must run the Greengrass CLI as an authorized user to interact with AWS IoT Greengrass Core software\. You can run it as one of the following users:
+ The root user \(`sudo`\)\.
+ A system user that runs AWS IoT Greengrass Core software\.
+ A system user in an authorized system group\. You use the `AuthorizedPosixGroups` configuration parameter when you deploy the Greengrass CLI component to authorize the system group\.

## Deploy the Greengrass CLI component<a name="gg-cli-deploy"></a>

Complete the following steps to deploy the Greengrass CLI component to your core device:

### To deploy the Greengrass CLI component \(console\)<a name="gg-cli-deploy-console"></a>

1. Sign in to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. In the navigation menu, choose **Components**\.

1. On the **Components** page, on the **Public components** tab, choose `aws.greengrass.Cli`\.

1. On the **aws\.greengrass\.Cli** page, choose **Deploy**\.

1. From **Add to deployment**, choose **Create new deployment**\.

1. On the **Specify target** page, under **Deployment targets**, in the **Target name** list, choose the Greengrass group that you want to deploy to, and choose **Next**\.

1. On the **Select components** page, verify that the `aws.greengrass.Cli` component is selected, and choose **Next**\.

1. On the **Configure components** page, keep the default configuration settings, and choose **Next**\.

1. On the **Configure advanced setting** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, click **Deploy**

### To deploy the Greengrass CLI component \(AWS CLI\)<a name="gg-cli-deploy-cli"></a>

1. On your device, create a `deployment.json` file to define the deployment configuration for the Greengrass CLI component\. This file should look like the following:

   ```
   {
     "targetArn":"targetArn",
     "components": {
       "aws.greengrass.Cli": {
         "componentVersion": "2.4.0",
         "configurationUpdate": {
           "AuthorizedPosixGroups": "<group1>,<group2>,...,<groupN>"
         }
       }
     }
   }
   ```
   + In the `target` field, replace `targetArn` with the Amazon Resource Name \(ARN\) of the thing or thing group to target for the deployment, in the following format: 
     + Thing: `arn:aws:iot:region:account-id:thing/thingName`
     + Thing group: `arn:aws:iot:region:account-id:thinggroup/thingGroupName`
   + In the `aws.greengrass.Cli` component object, specify values as follows:  
`version`  
The version of the Greengrass CLI component\.  
`configurationUpdate.AuthorizedPosixGroups`  
Optional\. A string that contains a comma\-separated list of system groups that you want to authorize to use the Greengrass CLI to interact with the AWS IoT Greengrass Core software\. You can specify group names or group IDs\. For example, specifying `"group1,1002,group3"` authorizes three system groups \(`group1`, `1002`, and `group3`\) to use the Greengrass CLI\.   
If you don't specify any groups to authorize, you can use the Greengrass CLI as the root user \(`sudo`\) or as the system user that runs the AWS IoT Greengrass Core software\. 

1. Run the following command to deploy the Greengrass CLI component on the device:

   ```
   $ aws greengrassv2 create-deployment \
       --cli-input-json file://path/to/deployment.json
   ```

During installation, the component adds a symbolic link to `greengrass-cli` in the `/greengrass/v2/bin` folder on your device, and you run the Greengrass CLI from this path\. To run the Greengrass CLI without its absolute path, add your `/greengrass/v2/bin` folder to your PATH variable\. To verify the Greengrass CLI installation, run the following command:

```
$ /greengrass/v2/bin/greengrass-cli help
```

You should see the following output:

```
Usage: greengrass-cli [-hV] [--ggcRootPath=<ggcRootPath>] [COMMAND]
Greengrass command line interface

      --ggcRootPath=<ggcRootPath>
                  The AWS IoT Greengrass V2 root directory.
  -h, --help      Show this help message and exit.
  -V, --version   Print version information and exit.
Commands:
  help                Show help information for a command.
  component           Retrieve component information and stop or restart
                        components.
  deployment          Create local deployments and retrieve deployment status.
  logs                Analyze Greengrass logs.
  get-debug-password  Generate a password for use with the HTTP debug view
                        component.
```

If the `greengrass-cli` isn't found, the deployment might have failed to install the Greengrass CLI\. For more information, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\.
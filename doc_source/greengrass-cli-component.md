# Greengrass CLI<a name="greengrass-cli-component"></a>

The Greengrass CLI component \(`aws.greengrass.Cli`\) provides a local command\-line interface that you can use on core devices to develop and debug components locally\. The Greengrass CLI lets you create local deployments and restart components on the core device, for example\.

You can install this component when you install the AWS IoT Greengrass Core software\. For more information, see [Getting started with AWS IoT Greengrass V2](getting-started.md)\.

After you install this component, run the following command to view its help documentation\. When this component installs, it adds a symbolic link to `greengrass-cli` in the `/greengrass/v2/bin` folder\. You can run the Greengrass CLI from this path or add it to your `PATH` environment variable to run `greengrass-cli` without its absolute path\.

```
/greengrass/v2/bin/greengrass-cli help
```

The following command restarts a component named `com.example.HelloWorld`, for example\.

```
sudo /greengrass/v2/bin/greengrass-cli component restart \
  --names "com.example.HelloWorld"
```

For more information, see [Greengrass Command Line Interface](gg-cli.md)\.

This component has the following versions:
+ 2\.0\.x

**Topics**
+ [Requirements](#greengrass-cli-component-requirements)
+ [Configuration](#greengrass-cli-component-configuration)
+ [Changelog](#greengrass-cli-component-changelog)

## Requirements<a name="greengrass-cli-component-requirements"></a>

This component has the following requirements:
+ You must be authorized to use the Greengrass CLI to interact with the AWS IoT Greengrass Core software\. Do one of the following to use the Greengrass CLI:
  + Use a root user \(`sudo`\)\.
  + Use the system user that runs the AWS IoT Greengrass Core software\.
  + Use a system user that's in a system group that you specify in the [AuthorizedPosixGroups](#greengrass-cli-component-configuration-authorizedposixgroups) configuration parameter\.

## Configuration<a name="greengrass-cli-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

  `AuthorizedPosixGroups`   
\(Optional\) A string that contains a comma\-separated list of system groups\. You authorize these system groups to use the Greengrass CLI to interact with the AWS IoT Greengrass Core software\. You can specify group names or group IDs\. For example, `group1,1002,group3` authorizes three system groups \(`group1`, `1002`, and `group3`\) to use the Greengrass CLI\.  
If you don't specify any groups to authorize, you can use the Greengrass CLI as the root user \(`sudo`\) or as the system user that runs the AWS IoT Greengrass Core software\.

## Changelog<a name="greengrass-cli-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  Version  |  Changes  | 
| --- | --- | 
|  2\.0\.4  | Version updated for Greengrass nucleus version 2\.0\.4 release\. | 
|  2\.0\.3  |  Initial version\.  | 
# Greengrass CLI commands<a name="gg-cli-reference"></a>

The Greengrass CLI provides a command line interface to interact locally with your AWS IoT Greengrass core device\. Greengrass CLI commands use the following format\.

```
$ greengrass-cli <command> <subcommand> [arguments]
```

By default, the `greengrass-cli` executable file in the `/greengrass/v2/bin/` folder interacts with the version of the AWS IoT Greengrass Core software running in the `/greengrass/v2` folder\. If you call an executable that is not placed in this location, or if you want to interact with AWS IoT Greengrass Core software in a different location, then you must use one of the following methods to explicitly specify the root path of the AWS IoT Greengrass Core software that you want to interact with:
+ Set the `GGC_ROOT_PATH` environment variable to `/greengrass/v2`\.
+ Add the `--ggcRootPath /greengrass/v2` argument to your command as shown in the following example\.

  ```
  greengrass-cli --ggcRootPath /greengrass/v2 <command> <subcommand> [arguments]
  ```

You can use the following arguments with any command:
+ Use `--help` for information about a specific Greengrass CLI command\. 
+ Use `--version` for information about the Greengrass CLI version\.

This section describes the Greengrass CLI commands and provides examples for these commands\. The synopsis for each command shows its arguments and their usage\. Optional arguments are shown in square brackets\.

**Topics**
+ [component](gg-cli-component.md)
+ [deployment](gg-cli-deployment.md)
+ [logs](gg-cli-logs.md)
+ [get\-debug\-password](gg-cli-get-debug-password.md)
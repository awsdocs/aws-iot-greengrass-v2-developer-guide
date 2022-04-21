# AWS IoT Greengrass Development Kit Command\-Line Interface commands<a name="greengrass-development-kit-cli-commands"></a>

The AWS IoT Greengrass Development Kit Command\-Line Interface \(GDK CLI\) provides a command line interface that you can use to create, build, and publish Greengrass components on your development computer\. GDK CLI commands use the following format\.

```
gdk <command> <subcommand> [arguments]
```

When you [install the GDK CLI](install-greengrass-development-kit-cli.md), the installer adds `gdk` to the PATH so you can run the GDK CLI from the command line\.

You can use the following arguments with any command:
+ Use `-h` or `--help` for information about a GDK CLI command\.
+ Use `-v` or `--version` to see what version of GDK CLI is installed\.
+ Use `-d` or `--debug` to output verbose logs that you can use to debug the GDK CLI\.

This section describes the GDK CLI commands and provides examples for each command\. The synopsis for each command shows its arguments and their usage\. Optional arguments are shown in square brackets\.

**Topics**
+ [component](greengrass-development-kit-cli-component.md)
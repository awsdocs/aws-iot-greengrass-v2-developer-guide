# Local debug console<a name="local-debug-console-component"></a>

The local debug console component \(`aws.greengrass.LocalDebugConsole`\) provides a local dashboard that displays information about your AWS IoT Greengrass core devices and its components\. You can use this dashboard to debug your core device and manage local components\.

**Important**  
Don't use this component in production environments\. This component is intended for use only in development environments\. Anyone with access to the AWS IoT Greengrass CLI on the core device can access information and perform operations that this component exposes\.

This component has the following versions:
+ 2\.0\.x

## Requirements<a name="local-debug-console-component-requirements"></a>

This component has the following requirements:
+ You use a user name and password to sign in to the dashboard\. The username, which is `debug`, is provided for you\. You must use the AWS IoT Greengrass CLI to create a temporary password that authenticates you with the dashboard on a core device\. You must be able to use the AWS IoT Greengrass CLI to use the local debug console\. For more information, see the [Greengrass CLI requirements](greengrass-cli-component.md#greengrass-cli-component-requirements)\. For more information about how to generate the password and sign in, see [Local debug console component usage](#local-debug-console-component-usage)\.

## Usage<a name="local-debug-console-component-usage"></a>

Follow these instructions to open the local debug console on your core device or on your development computer\.

**To open the local debug console**

1. \(Optional\) To view the local debug console on your development computer, you can forward the console's port over SSH\. This requires that you enable the `AllowTcpForwarding` option in your core device's SSH configuration file\. This option is enabled by default\. Run the following command on your development computer to view the dashboard at `http://localhost:1441` on your development computer\.

   ```
   ssh -L 1441:localhost:1441 -L 1442:localhost:1442 username@core-device-ip-address
   ```
**Note**  
You can change the default ports from `1441` and `1442`\. For more information, see [Local debug console configuration](#local-debug-console-component-configuration)\.

1. Generate a password to authenticate to the local dashboard\. The local debug console requires a password to increase security, because you can use this component to view important information and perform operations on the core device\.

   Use the AWS IoT Greengrass CLI to generate a random 43 character password that expires after 4 hours\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass V2 root folder\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli get-debug-password
   ```

   The debug view component creates a session lasts for 8 hours, and then you must generate a new password to view the local debug console again\. 

1. View the dashboard at `http://localhost:1441` on your Greengrass core device, or on your development computer if you forward the port over SSH\. The local debug console supports HTTP but doesn't support HTTPS\. Enter the password from the previous step to sign in and open the console\.

## Configuration<a name="local-debug-console-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`port`  
The port at which to provide the local debug console\.  
Default: `1441`

`websocketPort`  
The websocket port to use for the local debug console\.  
Default: `1442`
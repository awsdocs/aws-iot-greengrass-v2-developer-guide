# Local debug console<a name="local-debug-console-component"></a>

The local debug console component \(`aws.greengrass.LocalDebugConsole`\) provides a local dashboard that displays information about your AWS IoT Greengrass core devices and its components\. You can use this dashboard to debug your core device and manage local components\.

**Important**  
Don't use this component in production environments\. This component is intended for use only in development environments\. Anyone with access to the AWS IoT Greengrass CLI on the core device can access information and perform operations that this component exposes\.

**Topics**
+ [Versions](#local-debug-console-component-versions)
+ [Requirements](#local-debug-console-component-requirements)
+ [Dependencies](#local-debug-console-component-dependencies)
+ [Configuration](#local-debug-console-component-configuration)
+ [Usage](#local-debug-console-component-usage)
+ [Changelog](#local-debug-console-component-changelog)

## Versions<a name="local-debug-console-component-versions"></a>

This component has the following versions:
+ 2\.1\.x
+ 2\.0\.x

## Requirements<a name="local-debug-console-component-requirements"></a>

This component has the following requirements:
+ You use a user name and password to sign in to the dashboard\. The username, which is `debug`, is provided for you\. You must use the AWS IoT Greengrass CLI to create a temporary password that authenticates you with the dashboard on a core device\. You must be able to use the AWS IoT Greengrass CLI to use the local debug console\. For more information, see the [Greengrass CLI requirements](greengrass-cli-component.md#greengrass-cli-component-requirements)\. For more information about how to generate the password and sign in, see [Local debug console component usage](#local-debug-console-component-usage)\.

## Dependencies<a name="local-debug-console-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. You must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#local-debug-console-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.1\.x ]

The following table lists the dependencies for version 2\.1\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | \~2\.1\.0  | Hard | 
| [Greengrass CLI](greengrass-cli-component.md) | \~2\.1\.0 | Hard | 

------
#### [ 2\.0\.x ]

The following table lists the dependencies for version 2\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.3 <2\.1\.0  | Soft | 
| [Greengrass CLI](greengrass-cli-component.md) |  >=2\.0\.3 <2\.1\.0  | Soft | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="local-debug-console-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ v2\.1\.x ]

`httpsEnabled`  
\(Optional\) You can enable HTTPS communication for the local debug console\. If you enable HTTPS communication, the local debug console creates a self\-signed certificate\. Web browsers show security warnings for website that use self\-signed certificates, so you must manually verify the certificate and bypass the warning\. For more information, see [Usage](#local-debug-console-component-usage)\.  
Default: `true`

`port`  <a name="local-debug-console-component-configuration-port"></a>
\(Optional\) The port at which to provide the local debug console\.  
Default: `1441`

`websocketPort`  <a name="local-debug-console-component-configuration-websocket-port"></a>
\(Optional\) The websocket port to use for the local debug console\.  
Default: `1442`

------
#### [ v2\.0\.x ]

`port`  <a name="local-debug-console-component-configuration-port"></a>
\(Optional\) The port at which to provide the local debug console\.  
Default: `1441`

`websocketPort`  <a name="local-debug-console-component-configuration-websocket-port"></a>
\(Optional\) The websocket port to use for the local debug console\.  
Default: `1442`

------

## Usage<a name="local-debug-console-component-usage"></a>

To use the local debug console, create a session from the Greengrass CLI\. When you create a session, the Greengrass CLI provides a user name and temporary password that you can use to sign in to the local debug console\.

Follow these instructions to open the local debug console on your core device or on your development computer\.

------
#### [ v2\.1\.x ]

In version 2\.1\.x, the local debug console uses HTTPS by default\. When HTTPS is enabled, the local debug console creates a self\-signed certificate to secure the connection\. Your web browser shows a security warning when you open the local debug console because of this self\-signed certificate\. When you create a session with the Greengrass CLI, the output includes the certificate's fingerprints, so you can verify that the certificate is legitimate and the connection is secure\.

You can disable HTTPS\. For more information, see [Local debug console configuration](#local-debug-console-component-configuration)\.

**To open the local debug console**

1. <a name="local-debug-console-component-usage-forward-port"></a>\(Optional\) To view the local debug console on your development computer, you can forward the console's port over SSH\. However, you must first enable the `AllowTcpForwarding` option in your core device's SSH configuration file\. This option is enabled by default\. Run the following command on your development computer to view the dashboard at `http://localhost:1441` on your development computer\.

   ```
   ssh -L 1441:localhost:1441 -L 1442:localhost:1442 username@core-device-ip-address
   ```
**Note**  
You can change the default ports from `1441` and `1442`\. For more information, see [Local debug console configuration](#local-debug-console-component-configuration)\.

1. <a name="local-debug-console-component-create-session-step"></a>Create a session to use the local debug console\. When you create a session, you generate a password that you use to authenticate\. The local debug console requires a password to increase security, because you can use this component to view important information and perform operations on the core device\. The local debug console also creates a certificate to secure the connection if you enable HTTPS in the component configuration\. HTTPS is enabled by default\.

   Use the AWS IoT Greengrass CLI to create the session\. This command generates a random 43\-character password that expires after 8 hours\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass V2 root folder\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli get-debug-password
   ```

   The command output looks like the following example if you have configured the local debug console to use HTTPS\. You use the certificate fingerprints to verify that the connection is secure when you open the local debug console\.

   ```
   Username: debug
   Password: bEDp3MOHdj8ou2w5de_sCBI2XAaguy3a8XxREXAMPLE
   Password expires at: 2021-04-01T17:01:43.921999931-07:00
   The local debug console is configured to use TLS security. The certificate is self-signed so you will need to bypass your web browser's security warnings to open the console.
   Before you bypass the security warning, verify that the certificate fingerprint matches the following fingerprints.
   SHA-256: 15 0B 2C E2 54 8B 22 DE 08 46 54 8A B1 2B 25 DE FB 02 7D 01 4E 4A 56 67 96 DA A6 CC B1 D2 C4 1B
   SHA-1: BC 3E 16 04 D3 80 70 DA E0 47 25 F9 90 FA D6 02 80 3E B5 C1
   ```

   The debug view component creates a session that lasts for 8 hours\. After that, you must generate a new password to view the local debug console again\. 

1. Open and sign in to the dashboard\. View the dashboard on your Greengrass core device, or on your development computer if you forward the port over SSH\. Do one of the following:
   + If you enabled HTTPS in the local debug console, which is the default setting, do the following:

     1. Open `https://localhost:1441` on your core device, or on your development computer if you forwarded the port over SSH\.

        Your browser might show a security warning about an invalid security certificate\.

     1. If your browser shows a security warning, verify the certificate is legitimate and bypass the security warning\. Do the following:

        1. Find the SHA\-256 or SHA\-1 fingerprint for the certificate, and verify that it matches the SHA\-256 or SHA\-1 fingerprint that the `get-debug-password` command previously printed\. Your browser might provide one or both fingerprints\. Consult your browser's documentation to view the certificate and its fingerprints\. In some browsers, the certificate fingerprint is called a thumbprint\.
**Note**  
If the certificate fingerprint doesn't match, go to [Step 2](#local-debug-console-component-create-session-step) to create a new session\. If the certificate fingerprint still doesn't match, your connection might be insecure\.

        1. If the certificate fingerprint matches, bypass your browser's security warning to open the local debug console\. Consult your browser's documentation to bypass the browser security warning\.

     1. Sign in to the website using the user name and password that the `get-debug-password` command printed earlier\.

        The local debug console opens\.
   + If you disabled HTTPS in the local debug console, do the following:

     1. <a name="local-debug-console-component-usage-open-localhost-http"></a>Open `http://localhost:1441` on your core device, or open it on your development computer if you forwarded the port over SSH\.

     1. <a name="local-debug-console-component-usage-sign-in"></a>Sign in to the website using the user name and password that the `get-debug-password` command previously printed\.

        The local debug console opens\.

------
#### [ v2\.0\.x ]

**To open the local debug console**

1. <a name="local-debug-console-component-usage-forward-port"></a>\(Optional\) To view the local debug console on your development computer, you can forward the console's port over SSH\. However, you must first enable the `AllowTcpForwarding` option in your core device's SSH configuration file\. This option is enabled by default\. Run the following command on your development computer to view the dashboard at `http://localhost:1441` on your development computer\.

   ```
   ssh -L 1441:localhost:1441 -L 1442:localhost:1442 username@core-device-ip-address
   ```
**Note**  
You can change the default ports from `1441` and `1442`\. For more information, see [Local debug console configuration](#local-debug-console-component-configuration)\.

1. Create a session to use the local debug console\. When you create a session, you generate a password that you use to authenticate\. The local debug console requires a password to increase security, because you can use this component to view important information and perform operations on the core device\.

   Use the AWS IoT Greengrass CLI to create the session\. This command generates a random 43\-character password that expires after 8 hours\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass V2 root folder\.

   ```
   sudo /greengrass/v2/bin/greengrass-cli get-debug-password
   ```

   The command output looks like the following example\.

   ```
   Username: debug
   Password: bEDp3MOHdj8ou2w5de_sCBI2XAaguy3a8XxREXAMPLE
   Password will expire at: 2021-04-01T17:01:43.921999931-07:00
   ```

   The debug view component creates a session lasts for 4 hours, and then you must generate a new password to view the local debug console again\.

1. <a name="local-debug-console-component-usage-open-localhost-http"></a>Open `http://localhost:1441` on your core device, or open it on your development computer if you forwarded the port over SSH\.

1. <a name="local-debug-console-component-usage-sign-in"></a>Sign in to the website using the user name and password that the `get-debug-password` command previously printed\.

   The local debug console opens\.

------

## Changelog<a name="local-debug-console-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.1\.0  |  <a name="changelog-local-debug-console-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/local-debug-console-component.html)  | 
|  2\.0\.3  |  Initial version\.  | 
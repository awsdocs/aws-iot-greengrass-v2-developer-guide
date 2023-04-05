# Configure the AWS IoT Greengrass Core software<a name="configure-greengrass-core-v2"></a>

The AWS IoT Greengrass Core software provides options that you can use to configure the software\. You can create deployments  to configure the AWS IoT Greengrass Core software on each core device\.

**Topics**
+ [Deploy the Greengrass nucleus component](#configure-nucleus-component)
+ [Configure the Greengrass nucleus as a system service](#configure-system-service)
+ [Control memory allocation with JVM options](#jvm-tuning)
+ [Configure the user that runs components](#configure-component-user)
+ [Configure system resource limits for components](#configure-component-system-resource-limits)
+ [Connect on port 443 or through a network proxy](#configure-alpn-network-proxy)
+ [Use a device certificate signed by a private CA](#configure-nucleus-private-ca)
+ [Configure MQTT timeouts and cache settings](#configure-mqtt)

## Deploy the Greengrass nucleus component<a name="configure-nucleus-component"></a>

AWS IoT Greengrass provides the AWS IoT Greengrass Core software as a component that you can deploy to your Greengrass core devices\. You can create a deployment to apply the same configuration to multiple Greengrass core devices\. For more information, see [Greengrass nucleus](greengrass-nucleus-component.md) and [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.

## Configure the Greengrass nucleus as a system service<a name="configure-system-service"></a>

You must configure the AWS IoT Greengrass Core software as a system service in your device's init system to do the following:
+ Start the AWS IoT Greengrass Core software when the device boots\. This is a good practice if you manage large fleets of devices\.
+ Install and run plugin components\. Several AWS\-provided components are plugin components, which enables them to interface directly with the Greengrass nucleus\. For more information about component types, see [Component types](develop-greengrass-components.md#component-types)\.
+ Apply over\-the\-air \(OTA\) updates to the core device's AWS IoT Greengrass Core software\. For more information, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.
+ Enable components to restart the AWS IoT Greengrass Core software or the core device when a deployment updates the component to a new version or updates certain configuration parameters\. For more information, see the [bootstrap lifecycle step](component-recipe-reference.md#bootstrap-lifecycle-definition)\.

**Important**  <a name="windows-system-service-requirement-important-note"></a>
On Windows core devices, you must set up the AWS IoT Greengrass Core software as a system service\.

**Topics**
+ [Configure the nucleus as a system service \(Linux\)](#configure-system-service-linux)
+ [Configure the nucleus as a system service \(Windows\)](#configure-system-service-windows)

### Configure the nucleus as a system service \(Linux\)<a name="configure-system-service-linux"></a>

Linux devices support different init systems, such as initd, systemd, and SystemV\. You use the `--setup-system-service true` argument when you install the AWS IoT Greengrass Core software to start the nucleus as a system service and configure it to launch when the device boots\. The installer configures the AWS IoT Greengrass Core software as a system service with systemd\.

You can also manually configure the nucleus to run as a system service\. The following example is a service file for systemd\.

```
[Unit]
Description=Greengrass Core

[Service]
Type=simple
PIDFile=/greengrass/v2/alts/loader.pid
RemainAfterExit=no
Restart=on-failure
RestartSec=10
ExecStart=/bin/sh /greengrass/v2/alts/current/distro/bin/loader

[Install]
WantedBy=multi-user.target
```

After you configure the system service, you can run the following commands to configure starting the device on boot and to start or stop the AWS IoT Greengrass Core software\.
+ To check the status of the service \(systemd\)

  ```
  sudo systemctl status greengrass.service
  ```
+ To enable the nucleus to start when the device boots\.

  ```
  sudo systemctl enable greengrass.service
  ```
+ To stop the nucleus from starting when the device boots\.

  ```
  sudo systemctl disable greengrass.service
  ```
+ To start the AWS IoT Greengrass Core software\.

  ```
  sudo systemctl start greengrass.service
  ```
+ To stop the AWS IoT Greengrass Core software\.

  ```
  sudo systemctl stop greengrass.service
  ```

### Configure the nucleus as a system service \(Windows\)<a name="configure-system-service-windows"></a>

You use the `--setup-system-service true` argument when you install the AWS IoT Greengrass Core software to start the nucleus as a Windows service and configure it to launch when the device boots\.

After you configure the service, you can run the following commands to configure starting the device on boot and to start or stop the AWS IoT Greengrass Core software\. You must run Command Prompt or PowerShell as an administrator to run these commands\.

------
#### [ Windows Command Prompt \(CMD\) ]
+ To check the status of the service

  ```
  sc query "greengrass"
  ```
+ To enable the nucleus to start when the device boots\.

  ```
  sc config "greengrass" start=auto
  ```
+ To stop the nucleus from starting when the device boots\.

  ```
  sc config "greengrass" start=disabled
  ```
+ To start the AWS IoT Greengrass Core software\.

  ```
  sc start "greengrass"
  ```
+ To stop the AWS IoT Greengrass Core software\.

  ```
  sc stop "greengrass"
  ```
**Note**  <a name="windows-ignore-shutdown-signal-behavior-note"></a>
On Windows devices, the AWS IoT Greengrass Core software ignores this shutdown signal while it shuts down Greengrass component processes\. If the AWS IoT Greengrass Core software ignores the shutdown signal when you run this command, wait a few seconds, and try again\.

------
#### [ PowerShell ]
+ To check the status of the service

  ```
  Get-Service -Name "greengrass"
  ```
+ To enable the nucleus to start when the device boots\.

  ```
  Set-Service -Name "greengrass" -Status stopped -StartupType automatic
  ```
+ To stop the nucleus from starting when the device boots\.

  ```
  Set-Service -Name "greengrass" -Status stopped -StartupType disabled
  ```
+ To start the AWS IoT Greengrass Core software\.

  ```
  Start-Service -Name "greengrass"
  ```
+ To stop the AWS IoT Greengrass Core software\.

  ```
  Stop-Service -Name "greengrass"
  ```
**Note**  <a name="windows-ignore-shutdown-signal-behavior-note"></a>
On Windows devices, the AWS IoT Greengrass Core software ignores this shutdown signal while it shuts down Greengrass component processes\. If the AWS IoT Greengrass Core software ignores the shutdown signal when you run this command, wait a few seconds, and try again\.

------

## Control memory allocation with JVM options<a name="jvm-tuning"></a>

If you're running AWS IoT Greengrass on a device with limited memory, you can use Java virtual machine \(JVM\) options to control the maximum heap size, garbage collection modes, and compiler options, which control the amount of memory that AWS IoT Greengrass Core software uses\. The heap size in the JVM determines how much memory an application can use before [garbage collection](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/introduction.html) occurs, or before the application runs out of memory\. The maximum heap size specifies the maximum amount of memory the JVM can allocate when expanding the heap during heavy activity\. 

To control memory allocation, create a new deployment or revise an existing deployment that includes the nucleus component, and specify your JVM options in the `jvmOptions` configuration parameter in the [nucleus component configuration](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-jvm-options)\. 

Depending on your requirements, you can run AWS IoT Greengrass Core software with reduced memory allocation or with minimum memory allocation\. 

**Reduced memory allocation**  
To run AWS IoT Greengrass Core software with reduced memory allocation, we recommend that you use the following example configuration merge update to set JVM options in your nucleus configuration:

```
{
  "jvmOptions": "-Xmx64m -XX:+UseSerialGC -XX:TieredStopAtLevel=1"
}
```

**Minimum memory allocation**  
To run AWS IoT Greengrass Core software with minimum memory allocation, we recommend that you use the following example configuration merge update to set JVM options in your nucleus configuration:

```
{
  "jvmOptions": "-Xmx32m -XX:+UseSerialGC -Xint"
}
```

These example configuration merge updates use the following JVM options:

`-XmxNNm`  
Sets the maximum JVM heap size\.  
For reduced memory allocation, use `-Xmx64m` as a starting value to limit the heap size to 64 MB\. For minimum memory allocation, use `-Xmx32m` as a starting value to limit the heap size to 32 MB\.  
You can increase or decrease the `-Xmx` value depending on your actual requirements; however, we strongly recommend that you don't set the maximum heap size below 16 MB\. If the maximum heap size is too low for your environment, then the AWS IoT Greengrass Core software might encounter unexpected errors because of insufficient memory\.

`-XX:+UseSerialGC`  
Specifies to use serial garbage collection for JVM heap space\. The serial garbage collector is slower, but uses less memory than other JVM garbage collection implementations\.

`-XX:TieredStopAtLevel=1`  
Instructs the JVM to use the Java just\-in\-time \(JIT\) compiler once\. Because JIT compiled code uses space in the device memory, using the JIT compiler more than once consumes more memory than a single compilation\.

`-Xint`  
Instructs the JVM not to use the just\-in\-time \(JIT\) compiler\. Instead, the JVM runs in interpreted\-only mode\. This mode is slower than running JIT compiled code; however, the compiled code doesn't use any space in memory\.

For information about creating configuration merge updates, see [Update component configurations](update-component-configurations.md)\.

## Configure the user that runs components<a name="configure-component-user"></a>

The AWS IoT Greengrass Core software can run component processes as a system user and group different from the one that runs the software\. This increases security, because you can run the AWS IoT Greengrass Core software as root, or as an administrator user, without giving those permissions to components that run on the core device\.

The following table indicates which types of components the AWS IoT Greengrass Core software can run as a user that you specify\. For more information, see [Component types](develop-greengrass-components.md#component-types)\.


| Component type | Configure component user | 
| --- | --- | 
|  Nucleus  |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Plugin  |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Generic  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Lambda \(non\-containerized\)  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Lambda \(containerized\)  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 

You must create the component user before you can specify it in a deployment configuration\. On Windows\-based devices, you must also store the user name and password for the user in the credential manager instance of the LocalSystem account\. For more information, see [Set up a component user on Windows devices](#create-component-user-windows)\.

When you configure the component user on a Linux\-based device, you can optionally also specify a group\. You specify the user and group separated by a colon \(`:`\) in the following format: `user:group`\. If you don't specify a group, the AWS IoT Greengrass Core software defaults to the primary group of the user\. You can use either the name or the ID to identify the user and group\. 

On Linux\-based devices, you can also run components as a system user that doesn't exist, also called an unknown user, to increase security\. A Linux process can signal any other process that is run by the same user\. An unknown user doesn't run other processes, so you can run components as an unknown user to prevent components from signaling other components on the core device\. To run components as an unknown user, specify a user ID that doesn't exist on the core device\. You can also specify a group ID that doesn't exist to run as an unknown group\.

You can configure the user for each component and for each core device\.
+ **Configure for a component**

  You can configure each component to run with a user specific to that component\. When you create a deployment, you can specify the user for each component in the `runWith` configuration for that component\. The AWS IoT Greengrass Core software runs components as the specified user if you configure them\. Otherwise, it defaults to run components as the default user that you configure for the core device\. For more information about specifying the component user in the deployment configuration, see the [`runWith`](create-deployments.md#component-run-with-config) configuration parameter in [Create deployments](create-deployments.md)\.
+ **Configure default user for a core device**

  You can configure a default user that the AWS IoT Greengrass Core software uses to run components\. When the AWS IoT Greengrass Core software runs a component, it checks if you specified a user for that component, and uses it to run the component\. If the component doesn't specify a user, then the AWS IoT Greengrass Core software runs the component as the default user that you configured for the core device\. For more information, see [Configure the default component user](#configure-default-component-user)\.

**Note**  
On Windows\-based devices, you must specify at least a default user to run components\.  
On Linux\-based devices, the following considerations apply if you don't configure a user to run components:   
If you run the AWS IoT Greengrass Core software as root, then the software won't run components\. You must specify a default user to run components if you run as root\.
If you run the AWS IoT Greengrass Core software as a non\-root user, then the software runs components as that user\.

**Topics**
+ [Set up a component user on Windows devices](#create-component-user-windows)
+ [Configure the default component user](#configure-default-component-user)

### Set up a component user on Windows devices<a name="create-component-user-windows"></a>

**To set up a component user on a Windows\-based device**

1. Create the component user in the LocalSystem account on the device\.

   ```
   net user /add component-user password
   ```

1. Use [Microsoft's PsExec utility](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) to store the user name and password for the component user in the Credential Manager instance for the LocalSystem account\.

   ```
   psexec -s cmd /c cmdkey /generic:component-user /user:component-user /pass:password
   ```
**Note**  
On Windows\-based devices, the LocalSystem account runs the Greengrass nucleus, and you must use the PsExec utility to store the component user information in the LocalSystem account\. Using the Credential Manager application stores this information in the Windows account of the currently logged on user, instead of the LocalSystem account\.

### Configure the default component user<a name="configure-default-component-user"></a>

You can use a deployment to configure the default user on a core device\. In this deployment, you update the [nucleus component](greengrass-nucleus-component.md) configuration\.

**Note**  
You can also set the default user when you install the AWS IoT Greengrass Core software with the `--component-default-user` option\. For more information, see [Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\.

[Create a deployment](create-deployments.md) that specifies the following configuration update for the `aws.greengrass.Nucleus` component\.

------
#### [ Linux ]

```
{
  "runWithDefault": {
    "posixUser": "ggc_user:ggc_group"
  }
}
```

------
#### [ Windows ]

```
{
  "runWithDefault": {
    "windowsUser": "ggc_user"
  }
}
```

**Note**  
The user that you specify must exist, and the user name and password for this user must be stored in the credential manager instance of the LocalSystem account on your Windows device\. For more information, see [Set up a component user on Windows devices](#create-component-user-windows)\.

------

The following example defines a deployment for a Linux\-based device that configures `ggc_user` as the default user and `ggc_group` as the default group\. The `merge` configuration update requires a serialized JSON object\.

```
{
  "components": {
    "aws.greengrass.Nucleus": {
      "version": "2.9.4",
      "configurationUpdate": {
        "merge": "{\"runWithDefault\":{\"posixUser\":\"ggc_user:ggc_group\"}}"
      }
    }
  }
}
```

## Configure system resource limits for components<a name="configure-component-system-resource-limits"></a>

**Note**  
This feature is available for v2\.4\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 

You can configure the maximum amount of CPU and RAM usage that each component's processes can use on the core device\.

The following table shows the types of components that support system resource limits\. For more information, see [Component types](develop-greengrass-components.md#component-types)\.


| Component type | Configure system resource limits | 
| --- | --- | 
|  Nucleus  |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Plugin  |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 
|  Generic  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Lambda \(non\-containerized\)  |  <a name="polaris-yes-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-yes.png) Yes   | 
|  Lambda \(containerized\)  |  <a name="polaris-no-para"></a> ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/icon-no.png) No   | 

**Important**  
System resource limits aren't supported when you [run AWS IoT Greengrass Core software in a Docker container](run-greengrass-docker.md)\.

You can configure system resource limits for each component and for each core device\.
+ **Configure for a component**

  You can configure each component with system resource limits specific to that component\. When you create a deployment, you can specify the system resource limits for each component in the deployment\. If the component supports system resource limits, the AWS IoT Greengrass Core software applies the limits to the component's processes\. If you don't specify system resource limits for a component, the AWS IoT Greengrass Core software uses any defaults that you have configured for the core device\. For more information, see [Create deployments](create-deployments.md)\.
+ **Configure defaults for a core device**

  You can configure the default system resource limits that the AWS IoT Greengrass Core software applies to components that support these limits\. When the AWS IoT Greengrass Core software runs a component, it applies the system resource limits that you specify for that component\. If that component doesn't specify system resource limits, the the AWS IoT Greengrass Core software applies the default system resource limits that you configure for the core device\. If you don't specify default system resource limits, the AWS IoT Greengrass Core software doesn't apply any system resource limits by default\. For more information, see [Configure default system resource limits](#configure-default-component-system-resource-limits)\.

### Configure default system resource limits<a name="configure-default-component-system-resource-limits"></a>

You can deploy the [Greengrass nucleus component](greengrass-nucleus-component.md) to configure the default system resource limits for a core device\. To configure the default system resource limits, [create a deployment](create-deployments.md) that specifies the following configuration update for the `aws.greengrass.Nucleus` component\.

```
{
  "runWithDefault": {
    "systemResourceLimits": {
      "cpu": cpuTimeLimit,
      "memory": memoryLimitInKb
    }
  }
}
```

The following example defines a deployment that configures the CPU time limit to `2`, which is equivalent to 50% usage on a device with 4 CPU cores\. This example also configures the memory usage to 100 MB\.

```
{
  "components": {
    "aws.greengrass.Nucleus": {
      "version": "2.9.4",
      "configurationUpdate": {
        "merge": "{\"runWithDefault\":{\"systemResourceLimits\":\"cpu\":2,\"memory\":102400}}}"
      }
    }
  }
}
```

## Connect on port 443 or through a network proxy<a name="configure-alpn-network-proxy"></a>

AWS IoT Greengrass core devices communicate with AWS IoT Core using the MQTT messaging protocol with TLS client authentication\. By convention, MQTT over TLS uses port 8883\. However, as a security measure, restrictive environments might limit inbound and outbound traffic to a small range of TCP ports\. For example, a corporate firewall might open port 443 for HTTPS traffic, but close other ports that are used for less common protocols, such as port 8883 for MQTT traffic\. Other restrictive environments might require all traffic to go through a proxy before connecting to the internet\.

**Note**  
Greengrass core devices that run [Greengrass nucleus component](greengrass-nucleus-component.md) v2\.0\.3 and earlier use port 8443 to connect to the AWS IoT Greengrass data plane endpoint\. These devices must be able to connect to this endpoint on port 8443\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.

To enable communication in these scenarios, AWS IoT Greengrass provides the following configuration options:
+ **MQTT communication over port 443**\. If your network allows connections to port 443, you can configure the Greengrass core device to use port 443 for MQTT traffic instead of the default port 8883\. This can be a direct connection to port 443 or a connection through a network proxy server\. Unlike the default configuration, which uses certificate\-based client authentication, MQTT on port 443 uses the [device service role](device-service-role.md) for authentication\.

  For more information, see [Configure MQTT over port 443](#configure-mqtt-port-443)\.
+ **HTTPS communication over port 443**\. The AWS IoT Greengrass Core software sends HTTPS traffic over port 8443 by default, but you can configure it to use port 443\. AWS IoT Greengrass uses the [Application Layer Protocol Network](https://tools.ietf.org/html/rfc7301) \(ALPN\) TLS extension to enable this connection\. As with the default configuration, HTTPS on port 443 uses certificate\-based client authentication\.
**Important**  
To use ALPN and enable HTTPS communication over port 443, your core device must run Java 8 update 252 or later\. All updates of Java version 9 and later also support ALPN\.

  For more information, see [Configure HTTPS over port 443](#configure-https-port-443)\.
+ **Connection through a network proxy**\. You can configure a network proxy server to act as an intermediary for connecting to the Greengrass core device\. AWS IoT Greengrass supports basic authentication for HTTP and HTTPS proxies\.

  <a name="https-proxy-greengrass-nucleus-requirement"></a>Greengrass core devices must run [Greengrass nucleus ](greengrass-nucleus-component.md) v2\.5\.0 or later to use HTTPS proxies\.

  The AWS IoT Greengrass Core software passes the proxy configuration to components through the `ALL_PROXY`, `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables\. Components must use these settings to connect through the proxy\. Components use common libraries \(such as boto3, cURL, and the python `requests` package\) that typically use these environment variables by default to make connections\. If a component also specifies these environment variables, AWS IoT Greengrass doesn't override them\.

  For more information, see [Configure a network proxy](#configure-network-proxy)\.

### Configure MQTT over port 443<a name="configure-mqtt-port-443"></a>

You can configure MQTT over port 443 on existing core devices or when you install the AWS IoT Greengrass Core software on a new core device\.

**Topics**
+ [Configure MQTT over port 443 on existing core devices](#configure-mqtt-port-443-deployment)
+ [Configure MQTT over port 443 during installation](#configure-mqtt-port-443-installer)

#### Configure MQTT over port 443 on existing core devices<a name="configure-mqtt-port-443-deployment"></a>

You can use a deployment to configure MQTT over port 443 on a single core device or a group of core devices\. In this deployment, you update the [nucleus component](greengrass-nucleus-component.md) configuration\. The nucleus restarts when you update its `mqtt` configuration\.

To configure MQTT over port 443, [create a deployment](create-deployments.md) that specifies the following configuration update for the `aws.greengrass.Nucleus` component\.

```
{
  "mqtt": {
    "port": 443
  }
}
```

The following example defines a deployment that configures MQTT over port 443\. The `merge` configuration update requires a serialized JSON object\.

```
{
  "components": {
    "aws.greengrass.Nucleus": {
      "version": "2.9.4",
      "configurationUpdate": {
        "merge": "{\"mqtt\":{\"port\":443}}"
      }
    }
  }
}
```

#### Configure MQTT over port 443 during installation<a name="configure-mqtt-port-443-installer"></a>

You can configure MQTT over port 443 when you install the AWS IoT Greengrass Core software on a core device\. Use the `--init-config` installer argument to configure MQTT over port 443\. You can specify this argument when you install with [manual provisioning](manual-installation.md), [fleet provisioning](fleet-provisioning.md), or [custom provisioning](custom-provisioning.md)\.

### Configure HTTPS over port 443<a name="configure-https-port-443"></a>

This feature requires [Greengrass nucleus](greengrass-nucleus-component.md) v2\.0\.4 or later\.

You can configure HTTPS over port 443 on existing core devices or when you install the AWS IoT Greengrass Core software on a new core device\.

**Topics**
+ [Configure HTTPS over port 443 on existing core devices](#configure-https-port-443-deployment)
+ [Configure HTTPS over port 443 during installation](#configure-https-port-443-installer)

#### Configure HTTPS over port 443 on existing core devices<a name="configure-https-port-443-deployment"></a>

You can use a deployment to configure HTTPS over port 443 on a single core device or a group of core devices\. In this deployment, you update the [nucleus component](greengrass-nucleus-component.md) configuration\.

To configure HTTPS over port 443, [create a deployment](create-deployments.md) that specifies the following configuration update for the `aws.greengrass.Nucleus` component\.

```
{
  "greengrassDataPlanePort": 443
}
```

The following example defines a deployment that configures HTTPS over port 443\. The `merge` configuration update requires a serialized JSON object\.

```
{
  "components": {
    "aws.greengrass.Nucleus": {
      "version": "2.9.4",
      "configurationUpdate": {
        "merge": "{\"greengrassDataPlanePort\":443}"
      }
    }
  }
}
```

#### Configure HTTPS over port 443 during installation<a name="configure-https-port-443-installer"></a>

You can configure HTTPS over port 443 when you install the AWS IoT Greengrass Core software on a core device\. Use the `--init-config` installer argument to configure HTTPS over port 443\. You can specify this argument when you install with [manual provisioning](manual-installation.md), [fleet provisioning](fleet-provisioning.md), or [custom provisioning](custom-provisioning.md)\.

### Configure a network proxy<a name="configure-network-proxy"></a>

Follow a procedure in this section to configure Greengrass core devices to connect to the internet through an HTTP or HTTPS network proxy\. For more information about the endpoints and ports that core devices use, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.

**Important**  
If your core device runs a version of the [Greengrass nucleus](greengrass-nucleus-component.md) earlier than v2\.4\.0, your device's role must allow the following permissions to use a network proxy:  
`iot:Connect`
`iot:Publish`
`iot:Receive`
`iot:Subscribe`
This is necessary because the device uses AWS credentials from the token exchange service to authenticate MQTT connections to AWS IoT\. The device uses MQTT to receive and install deployments from the AWS Cloud, so your device won't work unless you define these permissions on its role\. Devices typically use X\.509 certificates to authenticate MQTT connections, but devices can't do this to authenticate when they use a proxy\.  
For more information about how to configure the device role, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

**Topics**
+ [Configure a network proxy on existing core devices](#configure-network-proxy-deployment)
+ [Configure a network proxy during installation](#configure-network-proxy-installer)
+ [Enable the core device to trust an HTTPS proxy](#https-proxy-certificate-trust)
+ [The networkProxy object](#network-proxy-object)

#### Configure a network proxy on existing core devices<a name="configure-network-proxy-deployment"></a>

You can use a deployment to configure a network proxy on a single core device or a group of core devices\. In this deployment, you update the [nucleus component](greengrass-nucleus-component.md) configuration\. The nucleus restarts when you update its `networkProxy` configuration\.

To configure a network proxy, [create a deployment](create-deployments.md) for the `aws.greengrass.Nucleus` component that merges the following configuration update\. This configuration update contains the [networkProxy object](#network-proxy-object)\.

```
{
  "networkProxy": {
    "noProxyAddresses": "http://192.168.0.1,www.example.com",
    "proxy": {
      "url": "https://my-proxy-server:1100",
      "username": "Mary_Major",
      "password": "pass@word1357"
    }
  }
}
```

The following example defines a deployment that configures a network proxy\. The `merge` configuration update requires a serialized JSON object\.

```
{
  "components": {
    "aws.greengrass.Nucleus": {
      "version": "2.9.4",
      "configurationUpdate": {
        "merge": "{\"networkProxy\":{\"noProxyAddresses\":\"http://192.168.0.1,www.example.com\",\"proxy\":{\"url\":\"https://my-proxy-server:1100\",\"username\":\"Mary_Major\",\"password\":\"pass@word1357\"}}}"
      }
    }
  }
}
```

#### Configure a network proxy during installation<a name="configure-network-proxy-installer"></a>

You can configure a network proxy when you install the AWS IoT Greengrass Core software on a core device\. Use the `--init-config` installer argument to configure the network proxy\. You can specify this argument when you install with [manual provisioning](manual-installation.md), [fleet provisioning](fleet-provisioning.md), or [custom provisioning](custom-provisioning.md)\.

#### Enable the core device to trust an HTTPS proxy<a name="https-proxy-certificate-trust"></a>

When you configure a core device to use an HTTPS proxy, you must add the proxy server certificate chain to the core device's to enable it to trust the HTTPS proxy\. Otherwise, the core device might encounter errors when it tries to route traffic through the proxy\. Add the proxy server CA certificate to the core device's Amazon root CA certificate file\.

**To enable the core device to trust the HTTPS proxy**

1. Find the Amazon root CA certificate file on the core device\.
   + If you installed the AWS IoT Greengrass Core software with [automatic provisioning](quick-installation.md), the Amazon root CA certificate file exists at `/greengrass/v2/rootCA.pem`\.
   + If you installed the AWS IoT Greengrass Core software with [manual](manual-installation.md) or [fleet provisioning](fleet-provisioning.md), the Amazon root CA certificate file might exist at `/greengrass/v2/AmazonRootCA1.pem`\.

   If the Amazon root CA certificate doesn't exist at these locations, check the `system.rootCaPath` property in `/greengrass/v2/config/effectiveConfig.yaml` to find its location\.

1. Add the contents of the proxy server CA certificate file to the Amazon root CA certificate file\.

   The following example shows a proxy server CA certificate added to the Amazon root CA certificate file\.

   ```
   -----BEGIN CERTIFICATE-----
   MIIEFTCCAv2gAwIQWgIVAMHSAzWG/5YVRYtRQOxXUTEpHuEmApzGCSqGSIb3DQEK
   \nCwUAhuL9MQswCQwJVUzEPMAVUzEYMBYGA1UECgwP1hem9uLmNvbSBJbmMuMRww
   ... content of proxy CA certificate ...
   +vHIRlt0e5JAm5\noTIZGoFbK82A0/nO7f/t5PSIDAim9V3Gc3pSXxCCAQoFYnui
   GaPUlGk1gCE84a0X\n7Rp/lND/PuMZ/s8YjlkY2NmYmNjMCAXDTE5MTEyN2cM216
   gJMIADggEPADf2/m45hzEXAMPLE=
   -----END CERTIFICATE-----
   
   -----BEGIN CERTIFICATE-----
   MIIDQTCCAimgF6AwIBAgITBmyfz/5mjAo54vB4ikPmljZKyjANJmApzyMZFo6qBg
   ADA5MQswCQYDVQQGEwJVUzEPMA0tMVT8QtPHRh8jrdkGA1UEChMGDV3QQDExBBKW
   ... content of root CA certificate ...
   o/ufQJQWUCyziar1hem9uMRkwFwYVPSHCb2XV4cdFyQzR1KldZwgJcIQ6XUDgHaa
   5MsI+yMRQ+hDaXJiobldXgjUka642M4UwtBV8oK2xJNDd2ZhwLnoQdeXeGADKkpy
   rqXRfKoQnoZsG4q5WTP46EXAMPLE
   -----END CERTIFICATE-----
   ```

#### The networkProxy object<a name="network-proxy-object"></a>

Use the `networkProxy` object to specify information about the network proxy\. This object contains the following information:

`noProxyAddresses`  
\(Optional\) A comma\-separated list of IP addresses or host names that are exempt from the proxy\.

`proxy`  
The proxy to which to connect\. This object contains the following information:    
`url`  
The URL of the proxy server in the format `scheme://userinfo@host:port`\.  <a name="nucleus-component-configuration-proxy-url-segments"></a>
+ `scheme` – The scheme, which must be `http` or `https`\.
**Important**  
<a name="https-proxy-greengrass-nucleus-requirement"></a>Greengrass core devices must run [Greengrass nucleus ](greengrass-nucleus-component.md) v2\.5\.0 or later to use HTTPS proxies\.  
If you configure an HTTPS proxy, you must add the proxy server CA certificate to the core device's Amazon root CA certificate\. For more information, see [Enable the core device to trust an HTTPS proxy](#https-proxy-certificate-trust)\.
+ `userinfo` – \(Optional\) The user name and password information\. If you specify this information in the `url`, the Greengrass core device ignores the `username` and `password` fields\.
+ `host` – The host name or IP address of the proxy server\.
+ `port` – \(Optional\) The port number\. If you don't specify the port, then the Greengrass core device uses the following default values:
  + `http` – 80
  + `https` – 443  
`username`  
\(Optional\) The user name that authenticates the proxy server\.  
`password`  
\(Optional\) The password that authenticates the proxy server\.

## Use a device certificate signed by a private CA<a name="configure-nucleus-private-ca"></a>

If you are using a custom private certificate authority \(CA\), you must set the Greengrass nucleus' **greengrassDataPlaneEndpoint** to **iotdata**\. You can set this option during deployment or installation using the **\-\-init\-config** [installer argument](configure-installer.md)\.

You can customize the Greengrass data plane endpoint where the device connects\. You can set this configuration option to **iotdata** to set the Greengrass data plane endpoint to the same endpoint as the IoT data endpoint, which you can specify with the **iotDataEndpoint**\.

## Configure MQTT timeouts and cache settings<a name="configure-mqtt"></a>

In the AWS IoT Greengrass environment, components can use MQTT to communicate with AWS IoT Core\. The AWS IoT Greengrass Core software manages MQTT messages for components\. When the core device loses connection to the AWS Cloud, the software caches MQTT messages to retry later when the connection restores\. You can configure settings such as message timeouts and the size of the cache\. For more information, see the `mqtt` and `mqtt.spooler` configuration parameters of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

AWS IoT Core imposes service quotas on its MQTT message broker\. These quotas might apply to messages that you send between core devices and AWS IoT Core\. For more information, see [AWS IoT Core message broker service quotas](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#message-broker-limits) in the *AWS General Reference*\.
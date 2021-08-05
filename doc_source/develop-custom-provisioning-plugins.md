# Develop custom provisioning plugins<a name="develop-custom-provisioning-plugins"></a>

To develop a custom provisioning plugin, create a Java class that implements the `com.aws.greengrass.provisioning.DeviceIdentityInterace` interface\. You can include the Greengrass nucleus JAR file in your project to access this interface and its classes\. This interface defines a method that inputs a plugin configuration and outputs a provisioning configuration\. The provisioning configuration defines configurations for the system and the [Greengrass nucleus component](greengrass-nucleus-component.md)\. The AWS IoT Greengrass Core software installer uses this provisioning configuration to configure the AWS IoT Greengrass Core software on a device\.

After you develop a custom provisioning plugin, build it as a JAR file that you can provide to the AWS IoT Greengrass Core software installer to run your plugin during installation\. The installer runs your custom provisioning plugin in the same JVM that the installer uses, so you can create a JAR that contains only your plugin code\.

**Topics**
+ [Implement the DeviceIdentityInterface interface](#implement-device-identity-interface)

## Implement the DeviceIdentityInterface interface<a name="implement-device-identity-interface"></a>

To use the `com.aws.greengrass.provisioning.DeviceIdentityInterface` interface in your custom plugin, add the Greengrass nucleus as a dependency to your project\.

**To use the DeviceIdentityInterface in a custom provisioning plugin project**
+ You can add the Greengrass nucleus JAR file as a library, or add the Greengrass nucleus as a Maven dependency\. Do one of the following:
  + To add the Greengrass nucleus JAR file as a library, download the AWS IoT Greengrass Core software, which contains the Greengrass nucleus JAR\. You can download the latest version of the AWS IoT Greengrass Core software from the following location:
    + [https://d2s8p88vqu9w66\.cloudfront\.net/releases/greengrass\-nucleus\-latest\.zip](https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip)

    You can find the Greengrass nucleus JAR file \(`Greengrass.jar`\) in the `lib` folder in the ZIP file\. Add this JAR file to your project\.
  + To consume the Greengrass nucleus in a Maven project, add a dependency the on the `nucleus` artifact in the `com.aws.greengrass` group\. You must also add the `greengrass-common` repository, because the Greengrass nucleus isn't available in the Maven Central Repository\.

    ```
    <project ...>
        ...
        <repositories>
            <repository>
                <id>greengrass-common</id>
                <name>greengrass common</name>
                <url>https://d2jrmugq4soldf.cloudfront.net/snapshots</url>
            </repository>
        </repositories>
        ...
        <dependencies>
            <dependency>
                <groupId>com.aws.greengrass</groupId>
                <artifactId>nucleus</artifactId>
                <version>2.4.0-SNAPSHOT</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
    </project>
    ```

### The DeviceIdentityInterface interface<a name="device-identity-interface-shape"></a>

The `com.aws.greengrass.provisioning.DeviceIdentityInterface` interface has the following shape\.

**Note**  
You can also explore these classes in the [Greengrass nucleus source code](https://github.com/aws-greengrass/aws-greengrass-nucleus/tree/main/src/main/java/com/aws/greengrass/provisioning) on GitHub\.

```
public interface com.aws.greengrass.provisioning.DeviceIdentityInterface {
    ProvisionConfiguration updateIdentityConfiguration(ProvisionContext context)
            throws RetryableProvisioningException, InterruptedException;

    // Return the name of the plugin.
    String name(); 
}

com.aws.greengrass.provisioning.ProvisionConfiguration {
    SystemConfiguration systemConfiguration;
    NucleusConfiguration nucleusConfiguration    
}

com.aws.greengrass.provisioning.ProvisionConfiguration.SystemConfiguration {
    String certificateFilePath;
    String privateKeyPath;
    String rootCAPath;
    String thingName;
}

com.aws.greengrass.provisioning.ProvisionConfiguration.NucleusConfiguration {
    String awsRegion;
    String iotCredentialsEndpoint;
    String iotDataEndpoint;
    String iotRoleAlias;
}

com.aws.greengrass.provisioning.ProvisioningContext {
    Map<String, Object> parameterMap;
    String provisioningPolicy;  // The policy is always "PROVISION_IF_NOT_PROVISIONED".
}
   
com.aws.greengrass.provisioning.exceptions.RetryableProvisioningException {}
```

Each configuration value in the `SystemConfiguration` and `NucleusConfiguration` is required to install the AWS IoT Greengrass Core software, but you can return `null`\. If your custom provisioning plugin returns `null` for any configuration value, you must provide that value in the system or nucleus configuration when you create the `config.yaml` file to provide to the AWS IoT Greengrass Core software installer\. If your custom provisioning plugin returns a non\-null value for an option that you also define in `config.yaml`, then the installer replaces the value in `config.yaml` with the value returned by the plugin\.
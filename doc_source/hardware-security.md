# Hardware security integration<a name="hardware-security"></a>

**Note**  
This feature is available for v2\.5\.3 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 

You can configure the AWS IoT Greengrass Core software to use a hardware security module \(HSM\) through the [PKCS\#11 interface](https://en.wikipedia.org/wiki/PKCS_11)\. This feature enables you to securely store the device's private key and certificate so that they aren't exposed or duplicated in software\. You can store the private key and certificate on a hardware module such as an HSM or a Trusted Platform Module \(TPM\)\.

The AWS IoT Greengrass Core software uses a private key and X\.509 certificate to authenticate connections to the AWS IoT and AWS IoT Greengrass services\. The [secret manager component](secret-manager-component.md) uses this private key to securely encrypt and decrypt the secrets that you deploy to a Greengrass core device\. When you configure a core device to use an HSM, these components use the private key and certificate that you store in the HSM\.

The [Moquette MQTT broker component](mqtt-broker-moquette-component.md) also stores a private key for its local MQTT server certificate\. This component store the private key on the device's file system in the component's work folder\. Currently, AWS IoT Greengrass doesn't support storing this private key or certificate in an HSM\.

**Tip**  
Search for devices that support this feature in the [AWS Partner Device Catalog](https://devices.amazonaws.com/search?kw=%22HSI%22&page=1)\.

**Topics**
+ [Requirements](#hardware-security-requirements)
+ [Hardware security best practices](#hardware-security-best-practices)
+ [Install the AWS IoT Greengrass Core software with hardware security](#install-with-hardware-security)
+ [Configure hardware security on an existing core device](#enable-hardware-security)
+ [Use hardware without PKCS\#11 support](#hardware-without-pkcs11)
+ [See also](#hardware-security-see-also)

## Requirements<a name="hardware-security-requirements"></a>

You must meet the following requirements to use an HSM on a Greengrass core device:
+ [Greengrass nucleus](greengrass-nucleus-component.md) v2\.5\.3 or later installed on the core device\. You can choose a compatible version when you install the AWS IoT Greengrass Core software on a core device\.
+ The [PKCS\#11 provider component](pkcs11-provider-component.md) installed on the core device\. You can download and install this component when you install the AWS IoT Greengrass Core software on a core device\.
+ <a name="hardware-security-module-requirements-key"></a>A hardware security module that supports the [PKCS\#1 v1\.5](https://tools.ietf.org/html/rfc2313) signature scheme and RSA keys with an RSA\-2048 key size \(or larger\) or ECC keys\.
**Note**  <a name="hardware-security-module-requirements-key-notes"></a>
To use a hardware security module with ECC keys, you must use [Greengrass nucleus](greengrass-nucleus-component.md) v2\.5\.6 or later\.  
To use a hardware security module and [secret manager](secret-manager-component.md), you must use a hardware security module with RSA keys\.
+ <a name="hardware-security-module-requirements-pkcs11-provider-library"></a>A PKCS\#11 provider library that the AWS IoT Greengrass Core software can load at runtime \(using libdl\) to invoke PKCS\#11 functions\. The PKCS\#11 provider library must implement the following PKCS\#11 API operations:
  + `C_Initialize`
  + `C_Finalize`
  + `C_GetSlotList`
  + `C_GetSlotInfo`
  + `C_GetTokenInfo`
  + `C_OpenSession`
  + `C_GetSessionInfo`
  + `C_CloseSession`
  + `C_Login`
  + `C_Logout`
  + `C_GetAttributeValue`
  + `C_FindObjectsInit`
  + `C_FindObjects`
  + `C_FindObjectsFinal`
  + `C_DecryptInit`
  + `C_Decrypt`
  + `C_DecryptUpdate`
  + `C_DecryptFinal`
  + `C_SignInit`
  + `C_Sign`
  + `C_SignUpdate`
  + `C_SignFinal`
  + `C_GetMechanismList`
  + `C_GetMechanismInfo`
  + `C_GetInfo`
  + `C_GetFunctionList`
+ <a name="hardware-security-module-requirements-slot-label"></a>The hardware module must be resolvable by slot label, as defined in the PKCS\#11 specification\.
+ <a name="hardware-security-module-requirements-private-key-and-certificate"></a>You must store the private key and certificate in the HSM in the same slot, and they must use the same object label and object ID, if the HSM supports object IDs\.
+ <a name="hardware-security-module-requirements-object-label"></a>The certificate and private key must be resolvable by object labels\.
+ <a name="hardware-security-module-requirements-private-key-permissions"></a>The private key must have the following permissions:
  + `sign`
  + `decrypt`
+ <a name="hardware-security-module-requirements-secret-manager-permissions"></a>\(Optional\) To use the [secret manager component](secret-manager-component.md), you must use version 2\.1\.0 or later, and the private key must have the following permissions:
  + `unwrap`
  + `wrap`

## Hardware security best practices<a name="hardware-security-best-practices"></a>

Consider the following best practices when you configure hardware security on Greengrass core devices\.
+ Generate private keys directly on the HSM by using the internal hardware random\-number generator\. This approach is more secure than importing a private key that you generate elsewhere, because the private key remains within the HSM\.
+ Configure private keys to be immutable and prohibit export\.
+ Use the provisioning tool that the HSM hardware vendor recommends to generate a certifacte signing request \(CSR\) using the hardware\-protected private key, and then use the AWS IoT console or API to generate a client certificate\.

**Note**  
The security best practice to rotate keys doesn't apply when you generate private keys on an HSM\.

## Install the AWS IoT Greengrass Core software with hardware security<a name="install-with-hardware-security"></a>

When you install the AWS IoT Greengrass Core software, you can configure it to use a private key that you generate in an HSM\. This approach follows the [security best practice](#hardware-security-best-practices) to generate the private key in the HSM, so the private key remains within the HSM\.

To install the AWS IoT Greengrass Core software with hardware security, you do the following:

1. Generate a private key in the HSM\.

1. Create a certificate signing request \(CSR\) from the private key\.

1. Create a certificate from the CSR\. You can create a certificate signed by AWS IoT or by another root certificate authority \(CA\)\. For more information about how to use another root CA, see [Create your own client certificates](https://docs.aws.amazon.com/iot/latest/developerguide/device-certs-your-own.html) in the *AWS IoT Core Developer Guide*\.

1. Download the AWS IoT certificate and import it into the HSM\.

1. Install the AWS IoT Greengrass Core software from a configuration file that specifies to use the PKCS\#11 provider component and the private key and certificate in the HSM\.

You can choose one of the following installation options to install the AWS IoT Greengrass Core software with hardware security:
+ **Manual installation**

  Choose this option to manually create the required AWS resources and configure hardware security\. For more information, see [Install AWS IoT Greengrass Core software with manual resource provisioning](manual-installation.md)\.
+ **Installation with custom provisioning**

  Choose this option to develop a custom Java application that automatically creates the required AWS resources and configures hardware security\. For more information, see [Install AWS IoT Greengrass Core software with custom resource provisioning](custom-provisioning.md)\.

Currently, AWS IoT Greengrass doesn't support installing the AWS IoT Greengrass Core software with hardware security when you [install with automatic resource provisioning](quick-installation.md) or [AWS IoT fleet provisioning](fleet-provisioning.md)\.

## Configure hardware security on an existing core device<a name="enable-hardware-security"></a>

You can import a core device's private key and certificate to an HSM to configure hardware security\.

**Considerations**  
You must have root access to the core device's file system\.
In this procedure, you shut down the AWS IoT Greengrass Core software, so the core device is offline and unavailable while you configure hardware security\.

To configure hardware security on an existing core device, you do the following:

1. Initialize the HSM\.

1. Deploy the [PKCS\#11 provider component](pkcs11-provider-component.md) to the core device\.

1. Stop the AWS IoT Greengrass Core software\.

1. Import the core device's private key and certificate to the HSM\.

1. Update the AWS IoT Greengrass Core software's configuration file to use the private key and certificate in the HSM\.

1. Start the AWS IoT Greengrass Core software\.

### Step 1: Initialize the hardware security module<a name="enable-hardware-security-initialize-hsm"></a>

Complete the following step to initialize the HSM on your core device\.

**To initialize the hardware security module**
+ Initialize an PKCS\#11 token in the HSM, and save the slot ID and user PIN that for the token\. Check the documentation for your HSM to learn how to initialize a token\. You use the slot ID and user PIN later when you deploy and configure the PKCS\#11 provider component\.

### Step 2: Deploy the PKCS\#11 provider component<a name="enable-hardware-security-deploy-pkcs11-provider"></a>

Complete the following steps to deploy and configure the [PKCS\#11 provider component](pkcs11-provider-component.md)\. You can deploy the component to one or more core devices\.

#### To deploy the PKCS\#11 provider component \(console\)<a name="deploy-pkcs11-provider-component-console"></a>

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

1. On the **Components** page, choose the **Public components** tab, and then choose **aws\.greengrass\.crypto\.Pkcs11Provider**\.

1. On the **aws\.greengrass\.crypto\.Pkcs11Provider** page, choose **Deploy**\.

1. <a name="deploy-component-choose-deployment-step"></a>From **Add to deployment**, choose an existing deployment to revise, or choose to create a new deployment, and then choose **Next**\.

1. <a name="deploy-component-choose-target-step"></a>If you chose to create a new deployment, choose the target core device or thing group for the deployment\. On the **Specify target** page, under **Deployment target**, choose a core device or thing group, and then choose **Next**\.

1. On the **Select components** page, under **Public components**, select **aws\.greengrass\.crypto\.Pkcs11Provider**, and then choose **Next**\.

1. On the **Configure components** page, select **aws\.greengrass\.crypto\.Pkcs11Provider**, and then do the following:

   1. Choose **Configure component**\.

   1. In the **Configure aws\.greengrass\.crypto\.Pkcs11Provider** modal, under **Configuration update**, in **Configuration to merge**, enter the following configuration update\. Update the following configuration parameters with values for the target core devices\. Specify the slot ID and user PIN where you initialized the PKCS\#11 token earlier\. You import the private key and certificate into this slot in the HSM later\.<a name="pkcs11-provider-component-configuration-parameters"></a>  
`name`  
A name for the PKCS\#11 configuration\.  
`library`  
The absolute file path to the PKCS\#11 implementation's library that the AWS IoT Greengrass Core software can load with libdl\.  
`slot`  
The ID of the slot that contains the private key and device certificate\. This value is different than the slot index or slot label\.  
`userPin`  
The user PIN to use to access the slot\.

      ```
      {
        "name": "softhsm_pkcs11",
        "library": "/usr/lib/softhsm/libsofthsm2.so",
        "slot": 1,
        "userPin": "1234"
      }
      ```

   1. Choose **Confirm** to close the modal, and then choose **Next**\.

1. <a name="deploy-component-configure-advanced-settings-step"></a>On the **Configure advanced settings** page, keep the default configuration settings, and choose **Next**\.

1. <a name="deploy-component-review-and-deploy-step"></a>On the **Review** page, choose **Deploy**\.

   The deployment can take up to a minute to complete\.

#### To deploy the PKCS\#11 provider component \(AWS CLI\)<a name="deploy-pkcs11-provider-component-cli"></a>

To deploy the PKCS\#11 provider component, create a deployment document that includes `aws.greengrass.crypto.Pkcs11Provider` in the `components` object, and specify the configuration update for the component\. Follow instructions in [Create deployments](create-deployments.md) to create a new deployment or revise an existing deployment\.

The following example partial deployment document specifies to deploy and configure the PKCS\#11 provider component\. Update the following configuration parameters with values for the target core devices\. Save the slot ID and user PIN to use later when you import the private key and certificate into the HSM\.<a name="pkcs11-provider-component-configuration-parameters"></a>

`name`  
A name for the PKCS\#11 configuration\.

`library`  
The absolute file path to the PKCS\#11 implementation's library that the AWS IoT Greengrass Core software can load with libdl\.

`slot`  
The ID of the slot that contains the private key and device certificate\. This value is different than the slot index or slot label\.

`userPin`  
The user PIN to use to access the slot\.

```
{
  "name": "softhsm_pkcs11",
  "library": "/usr/lib/softhsm/libsofthsm2.so",
  "slot": 1,
  "userPin": "1234"
}
```

```
{
  ...,
  "components": {
    ...,
    "aws.greengrass.crypto.Pkcs11Provider": {
      "componentVersion": "2.0.0",
      "configurationUpdate": {
        "merge": "{\"name\":\"softhsm_pkcs11\",\"library\":\"/usr/lib/softhsm/libsofthsm2.so\",\"slot\":1,\"userPin\":\"1234\"}"
      }
    }
  }
}
```

The deployment can take several minutes to complete\. You can use the AWS IoT Greengrass service to check the status of the deployment\. You can check the AWS IoT Greengrass Core software logs to verify that the PKCS\#11 provider component deploys successfully\. For more information, see the following:
+ [Check deployment status](check-deployment-status.md)
+ [Monitor AWS IoT Greengrass logs](monitor-logs.md)

If the deployment fails, you can troubleshoot the deployment on each core device\. For more information, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\.

### Step 3: Update the configuration on the core device<a name="enable-hardware-security-configure-core-device"></a>

The AWS IoT Greengrass Core software uses a configuration file that specifies how the device operates\. This configuration file includes where to find the private key and certificate that the device uses to connect to the AWS Cloud\. Complete the following steps to import the core device's private key and certificate into the HSM and update the configuration file to use the HSM\.

**To update the configuration on the core device to use hardware security**

1. Stop the AWS IoT Greengrass Core software\. If you [configured the AWS IoT Greengrass Core software as a system service](configure-greengrass-core-v2.md#configure-system-service) with systemd, you can run the following command to stop the software\.

   ```
   sudo systemctl stop greengrass.service
   ```

1. Find the core device's private key and certificate files\.
   + If you installed the AWS IoT Greengrass Core software with [automatic provisioning](quick-installation.md) or [fleet provisioning](fleet-provisioning.md), the private key exists at `/greengrass/v2/privKey.key`, and the certificate exists at `/greengrass/v2/thingCert.crt`\.
   + If you installed the AWS IoT Greengrass Core software with [manual provisioning](manual-installation.md), the private key exists at `/greengrass/v2/private.pem.key` by default, and the certificate exists at `/greengrass/v2/device.pem.crt` by default\.

   You can also check the `system.privateKeyPath` and `system.certificateFilePath` properties in `/greengrass/v2/config/effectiveConfig.yaml` to find the location of these files\.

1. Import the private key and certificate into the HSM\. Check the documentation for your HSM to learn how to import private keys and certificates into it\. Import the private key and certificate using the slot ID and user PIN where you initialized the PKCS\#11 token earlier\. You must use the same object label and object ID for the private key and the certificate\. Save the object label that you specify when you import each file\. You use this label later when you update the AWS IoT Greengrass Core software configuration to use the private key and certificate in the HSM\.

1. Update the AWS IoT Greengrass Core configuration to use the private key and certificate in the HSM\. To update the configuration, you modify the AWS IoT Greengrass Core configuration file and run the AWS IoT Greengrass Core software with the updated configuration file to apply the new configuration\.

   Do the following:

   1. Create a back up of the AWS IoT Greengrass Core configuration file\. You can use this back up to restore the core device if you run into issues when you configure hardware security\.

      ```
      sudo cp /greengrass/v2/config/effectiveConfig.yaml ~/ggc-config-backup.yaml
      ```

   1. Open the AWS IoT Greengrass Core configuration file in a text editor\. For example, you can run the following command to use GNU nano to edit the file\. Replace */greengrass/v2* with the path to the Greengrass root folder\.

      ```
      sudo nano /greengrass/v2/config/effectiveConfig.yaml
      ```

   1. Replace the value of the `system.privateKeyPath` with the PKCS\#11 URI for the private key in the HSM\. Replace *iotdevicekey* with the object label where you imported the private key and certificate earlier\.

      ```
      pkcs11:object=iotdevicekey;type=private
      ```

   1. Replace the value of the `system.certificateFilePath` with the PKCS\#11 URI for the certificate in the HSM\. Replace *iotdevicekey* with the object label where you imported the private key and certificate earlier\.

      ```
      pkcs11:object=iotdevicekey;type=cert
      ```

   After you finish these steps, the `system` property in the AWS IoT Greengrass Core configuration file should look similar to the following example\.

   ```
   system:
     certificateFilePath: "pkcs11:object=iotdevicekey;type=cert"
     privateKeyPath: "pkcs11:object=iotdevicekey;type=private"
     rootCaPath: "/greengrass/v2/rootCA.pem"
     rootpath: "/greengrass/v2"
     thingName: "MyGreengrassCore"
   ```

1. Apply the configuration in the updated `effectiveConfig.yaml` file\. Run `Greengrass.jar` with the `--init-config` parameter to apply the configuration in `effectiveConfig.yaml`\. Replace */greengrass/v2* with the path to the Greengrass root folder\.

   ```
   sudo java -Droot="/greengrass/v2" \
     -jar /greengrass/v2/alts/current/distro/lib/Greengrass.jar \
     --start false \
     --init-config /greengrass/v2/config/effectiveConfig.yaml
   ```

1. Start the AWS IoT Greengrass Core software\. If you [configured the AWS IoT Greengrass Core software as a system service](configure-greengrass-core-v2.md#configure-system-service) with systemd, you can run the following command to start the software\.

   ```
   sudo systemctl start greengrass.service
   ```

   For more information, see [Run the AWS IoT Greengrass Core software](run-greengrass-core-v2.md)\.

1. Check the AWS IoT Greengrass Core software logs to verify that the software starts and connects to the AWS Cloud\. The AWS IoT Greengrass Core software uses the private key and certificate to connect to the AWS IoT and AWS IoT Greengrass services\.

   ```
   sudo tail -f /greengrass/v2/logs/greengrass.log
   ```

   The following INFO\-level log messages indicate that the AWS IoT Greengrass Core software successfully connects to the AWS IoT and AWS IoT Greengrass services\.

   ```
   2021-12-06T22:47:53.702Z [INFO] (Thread-3) com.aws.greengrass.mqttclient.AwsIotMqttClient: Successfully connected to AWS IoT Core. {clientId=MyGreengrassCore5, sessionPresent=false}
   ```

1. \(Optional\) After you verify that the AWS IoT Greengrass Core software works with the private key and certificate in the HSM, delete the private key and certificate files from the device's file system\. Run the following command, and replace the file paths with the paths to the private key and certificate files\.

   ```
   sudo rm /greengrass/v2/privKey.key
   sudo rm /greengrass/v2/thingCert.crt
   ```

## Use hardware without PKCS\#11 support<a name="hardware-without-pkcs11"></a>

The PKCS\#11 library is typically provided by the hardware vendor or is open source\. For example, with standards\-compliant hardware \(such as TPM1\.2\), it might be possible to use existing open source software\. However, if your hardware doesn't have a corresponding PKCS\#11 library implementation, or if you want to write a custom PKCS\#11 provider, contact your Amazon Web Services Enterprise Support representative with integration\-related questions\.

## See also<a name="hardware-security-see-also"></a>
+ [PKCS \#11 Cryptographic Token Interface Usage Guide Version 2\.4\.0](http://docs.oasis-open.org/pkcs11/pkcs11-ug/v2.40/pkcs11-ug-v2.40.html)
+ [RFC 7512](https://tools.ietf.org/html/rfc7512)
+ [PKCS \#1: RSA Encryption Version 1\.5](https://tools.ietf.org/html/rfc2313)
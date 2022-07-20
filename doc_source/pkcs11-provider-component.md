# PKCS\#11 provider<a name="pkcs11-provider-component"></a>

The PKCS\#11 provider component \(`aws.greengrass.crypto.Pkcs11Provider`\) enables you to configure the AWS IoT Greengrass Core software to use a hardware security module \(HSM\) through the [PKCS\#11 interface](https://en.wikipedia.org/wiki/PKCS_11)\. This component enables you to securely store certificate and private key files so that they aren't exposed or duplicated in software\. For more information, see [Hardware security integration](hardware-security.md)\.

To provision a Greengrass core device that stores its certificate and private key in an HSM, you must specify this component as a provisioning plugin when you install the AWS IoT Greengrass Core software\. For more information, see [Install AWS IoT Greengrass Core software with manual resource provisioning](manual-installation.md)\.

AWS IoT Greengrass provides this component as JAR file that you can download to specify as a provisioning plugin during installation\. You can download the latest version of the component's JAR file as the following URL: [https://d2s8p88vqu9w66\.cloudfront\.net/releases/Pkcs11Provider/aws\.greengrass\.crypto\.Pkcs11Provider\-latest\.jar](https://d2s8p88vqu9w66.cloudfront.net/releases/Pkcs11Provider/aws.greengrass.crypto.Pkcs11Provider-latest.jar)\.

**Topics**
+ [Versions](#pkcs11-provider-component-versions)
+ [Type](#pkcs11-provider-component-type)
+ [Operating system](#pkcs11-provider-component-os-support)
+ [Requirements](#pkcs11-provider-component-requirements)
+ [Dependencies](#pkcs11-provider-component-dependencies)
+ [Configuration](#pkcs11-provider-component-configuration)
+ [Local log file](#pkcs11-provider-component-log-file)
+ [Changelog](#pkcs11-provider-component-changelog)

## Versions<a name="pkcs11-provider-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="pkcs11-provider-component-type"></a>

<a name="public-component-type-plugin-para1"></a>This component is a plugin component \(`aws.greengrass.plugin`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component in the same Java Virtual Machine \(JVM\) as the nucleus\. The nucleus restarts when you change this component's version on the core device\.

<a name="public-component-type-plugin-para2"></a>This component uses the same log file as the Greengrass nucleus\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="pkcs11-provider-component-os-support"></a>

This component can be installed on Linux core devices only\.

## Requirements<a name="pkcs11-provider-component-requirements"></a>

This component has the following requirements:
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

## Dependencies<a name="pkcs11-provider-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#pkcs11-provider-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.0\.1 ]

The following table lists the dependencies for version 2\.0\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.5\.3 <2\.7\.0  | Soft | 

------
#### [ 2\.0\.0 ]

The following table lists the dependencies for version 2\.0\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.5\.3 <2\.6\.0  | Soft | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="pkcs11-provider-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.<a name="pkcs11-provider-component-configuration-parameters"></a>

`name`  
A name for the PKCS\#11 configuration\.

`library`  
The absolute file path to the PKCS\#11 implementation's library that the AWS IoT Greengrass Core software can load with libdl\.

`slot`  
The ID of the slot that contains the private key and device certificate\. This value is different than the slot index or slot label\.

`userPin`  
The user PIN to use to access the slot\.

**Example: Configuration merge update**  

```
{
  "name": "softhsm_pkcs11",
  "library": "/usr/lib/softhsm/libsofthsm2.so",
  "slot": 1,
  "userPin": "1234"
}
```

## Local log file<a name="pkcs11-provider-component-log-file"></a>

This component uses the same log file as the [Greengrass nucleus](greengrass-nucleus-component.md) component\.

------
#### [ Linux ]

```
/greengrass/v2/logs/greengrass.log
```

------
#### [ Windows ]

```
C:\greengrass\v2\logs\greengrass.log
```

------

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux ]

  ```
  sudo tail -f /greengrass/v2/logs/greengrass.log
  ```

------
#### [ Windows \(PowerShell\) ]

  ```
  Get-Content C:\greengrass\v2\logs\greengrass.log -Tail 10 -Wait
  ```

------

## Changelog<a name="pkcs11-provider-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.1  |  Version updated for Greengrass nucleus version 2\.6\.0 release\.  | 
|  2\.0\.0  |  Initial version\.  | 
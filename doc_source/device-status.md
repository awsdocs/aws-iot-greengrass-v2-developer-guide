# Check Greengrass core device status<a name="device-status"></a>

Greengrass core devices report the status of their software components to AWS IoT Greengrass\. You can check the health summary of each device, and you can check the status of each component on each device\.

Core devices have the following health statuses:
+ `HEALTHY` – The AWS IoT Greengrass Core software and all components run without issue on the core device\.
+ `UNHEALTHY` – The AWS IoT Greengrass Core software or a component is in an error state on the core device\.

**Note**  
AWS IoT Greengrass relies on individual devices to send status updates to the AWS Cloud\. If the AWS IoT Greengrass Core software isn't running on the device, or if device isn't connected to the AWS Cloud, then the reported status of that device might not reflect its current status\. The status timestamp indicates when the device status was last updated\.  
Core devices send status updates at the following times:  
When the AWS IoT Greengrass Core software starts
When the core device receives a deployment from the AWS Cloud
When the status of any component on the core device becomes `BROKEN`
At a [regular interval that you can configure](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-fss), which defaults to 24 hours

**Topics**
+ [Check health of a core device](#check-core-device-health-status)
+ [Check health of a core device group](#check-core-device-group-health-status)
+ [Check core device component status](#check-core-device-component-status)

## Check health of a core device<a name="check-core-device-health-status"></a>

You can check the status of individual core devices\.

**To check the status of a core device \(AWS CLI\)**
+ Run the following command to retrieve the status of a device\. Replace *coreDeviceName* with the name of the core device to query\.

  ```
  aws greengrassv2 get-core-device --core-device-thing-name coreDeviceName
  ```

  The response contains information about the core device, including its status\.

## Check health of a core device group<a name="check-core-device-group-health-status"></a>

You can check the status of a group of core devices \(a thing group\)\.

**To check the status of a group of devices \(AWS CLI\)**
+ Run the following command to retrieve the status of multiple core devices\. Replace the ARN in the command with the ARN of the thing group to query\.

  ```
  aws greengrassv2 list-core-devices --thing-group-arn "arn:aws:iot:region:account-id:thinggroup/thingGroupName"
  ```

  The response contains the list of core devices in the thing group\. Each entry in the list contains the status of the core device\.

## Check core device component status<a name="check-core-device-component-status"></a>

You can check the status, such as lifecycle state, of the software components on a core device\. For more information about component lifecycle states, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md)\.

**To check the status of components on a core device \(AWS CLI\)**
+ Run the following command to retrieve the status of the components on a core device\. Replace *coreDeviceName* with the name of the core device to query\.

  ```
  aws greengrassv2 list-installed-components --core-device-thing-name coreDeviceName
  ```

  The response contains the list of components that run on the core device\. Each entry in the list contains the lifecycle state of the component\.
**Note**  
This list doesn't include components that are deployed from local deployments or components that are deployed as dependencies of other components\.
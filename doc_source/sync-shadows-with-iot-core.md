# Sync local device shadows with AWS IoT Core<a name="sync-shadows-with-iot-core"></a>

The shadow manager component enables AWS IoT Greengrass to sync local device shadow states with AWS IoT Core\. You must modify the configuration of the shadow manager component to include the `synchronization` configuration parameter, and specify the AWS IoT thing names for your devices, and the shadows that you want to sync\. 

When you configure shadow manager to sync shadows, it syncs all state changes for specified shadows, regardless of whether the changes occur in local shadow documents or in cloud shadow documents\.

You can also specify whether the shadow manager component syncs shadows in real time or on a periodic interval\. By default, the shadow manager component syncs shadows in real time, so the core device sends and receives shadow updates to and from AWS IoT Core when each update occurs\. You can configure periodic intervals to reduce bandwidth usage and charges\.

**Topics**
+ [Prerequisites](#shadow-sync-prereqs)
+ [Configure the shadow manager component](#configure-shadow-manager-for-sync)
+ [Sync local shadows](#sync-local-shadows)
+ [Shadow merge conflict behavior](#shadow-merge-behavior)

## Prerequisites<a name="shadow-sync-prereqs"></a>

To sync local shadows to the AWS IoT Core, you must configure the Greengrass core device's AWS IoT policy to allow the following AWS IoT Core shadow policy actions\.
+ `iot:GetThingShadow`
+ `iot:UpdateThingShadow`
+ `iot:DeleteThingShadow`

For more information, see the following:
+ [AWS IoT Core policy actions](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policy-actions.html) in the *AWS IoT Developer Guide*
+ [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy)
+ [Update a core device's AWS IoT policy](device-auth.md#update-core-device-iot-policy)

## Configure the shadow manager component<a name="configure-shadow-manager-for-sync"></a>

The shadow manager requires a list of shadow name mappings to sync shadow state information in local shadow documents to cloud shadow documents in AWS IoT Core\.

To sync shadow states, [create a deployment](create-deployments.md) that includes the `aws.greengrass.ShadowManager` component, and specify the shadows that you want to sync in the `synchronize` configuration parameter in the shadow manager configuration in the deployment\.

The following example configuration update instructs the shadow manager component to sync the following shadows with AWS IoT Core:
+ The classic shadow for the core device 
+ The named `MyCoreShadow` for the core device 
+ The classic shadow for an IoT thing named `MyDevice2` 
+ The named shadows `MyShadowA` and `MyShadowB` for a IoT thing named `MyDevice1`

This configuration update specifies to sync shadows with AWS IoT Core in real time\. To configure the shadow manager component to sync shadows on a periodic interval, change the sync strategy to `periodic` and specify a `delay` in seconds for the interval\. For more information, see [the strategy configuration parameter](shadow-manager-component.md#shadow-manager-component-configuration-strategy) of the shadow manager component\.

```
{
  "strategy": {
    "type": "realTime"
  },
  "synchronize": {
    "coreThing": {
      "classic": true,
      "namedShadows": [
        "MyCoreShadow"
      ]
    },
    "shadowDocuments": [
      {
        "thingName": "MyDevice1",
        "classic": false,
        "namedShadows": [
          "MyShadowA",
          "MyShadowB"
        ]
      },
      {
        "thingName": "MyDevice2",
        "classic": true,
        "namedShadows": [ ]
      }
    ]
  }
}
```

## Sync local shadows<a name="sync-local-shadows"></a>

When the Greengrass core device is connected to the AWS IoT cloud, the shadow manager performs the following tasks for the shadows that you specify in the component configuration:
+ Retrieves the reported state information from the cloud shadow document in AWS IoT Core\. 
+ Updates locally stored shadow documents to synchronize the device state\. 
+ Publishes the device's current state to the cloud shadow document\.

## Shadow merge conflict behavior<a name="shadow-merge-behavior"></a>

In some cases, such as when the core device is disconnected from the internet, a shadow might change in the local shadow service and in the AWS IoT cloud before the shadow manager synchronizes the changes\. As a result, the desired and reported states differ between the local shadow service and the AWS IoT cloud\. When the shadow manager synchronizes the shadow, it merges the changes according to the following behavior:
+ When there's a merge conflict in a shadow's desired state, the shadow manager overwrites the conflicting section of the local shadow document with the value from the AWS IoT cloud\.
+ When there's a merge conflict in a shadow's reported state, the shadow manager overwrites the conflicting section of the shadow in the AWS IoT cloud with the value from the local shadow document\.
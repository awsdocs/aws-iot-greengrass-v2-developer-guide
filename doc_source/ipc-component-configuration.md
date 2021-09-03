# Interact with component configuration<a name="ipc-component-configuration"></a>

The component configuration IPC service lets you do the following:
+ Get and set component configuration parameters\.
+ Subscribe to component configuration updates\.
+ Validate component configuration updates before the nucleus applies them\.

**Topics**
+ [GetConfiguration](#ipc-operation-getconfiguration)
+ [UpdateConfiguration](#ipc-operation-updateconfiguration)
+ [SubscribeToConfigurationUpdate](#ipc-operation-subscribetoconfigurationupdate)
+ [SubscribeToValidateConfigurationUpdates](#ipc-operation-subscribetovalidateconfigurationupdates)
+ [SendConfigurationValidityReport](#ipc-operation-sendconfigurationvalidityreport)

## GetConfiguration<a name="ipc-operation-getconfiguration"></a>

Gets a configuration value for a component on the core device\. You specify the key path for which to get a configuration value\.

### Request<a name="ipc-operation-getconfiguration-request"></a>

This operation's request has the following parameters:

`componentName` \(Python: `component_name`\)  <a name="ipc-configuration-request-component-name"></a>
\(Optional\) The name of the component\.  
Defaults to the name of the component that makes the request\.

`keyPath`  
The key path to the configuration value\. Specify a list where each entry is the key for a single level in the configuration object\. For example, specify `["mqtt", "port"]` to get the value of `port` in the following configuration\.  

```
{
  "mqtt": {
    "port": 443
  }
}
```

### Response<a name="ipc-operation-getconfiguration-response"></a>

This operation's response has the following information:

`componentName` \(Python: `component_name`\)  <a name="ipc-configuration-response-component-name"></a>
The name of the component\.

`value`  
The requested configuration as an object\.

## UpdateConfiguration<a name="ipc-operation-updateconfiguration"></a>

Updates a configuration value for a component on the core device\.

### Request<a name="ipc-operation-updateconfiguration-request"></a>

This operation's request has the following parameters:

`keyPath` \(Python: `key_path`\)  
\(Optional\) The key path to the container node \(the object\) to update\. Specify a list where each entry is the key for a single level in the configuration object\. For example, specify the key path `["mqtt"]` and the merge value `{ "port": 443 }` to set the value of `port` in the following configuration\.  

```
{
  "mqtt": {
    "port": 443
  }
}
```
The key path must specify a container node \(an object\) in the configuration\. If the node doesn't exist in the component's configuration, this operation creates it and sets its value to the object in `valueToMerge`\.  
Defaults to the root of the configuration object\.

`timestamp`  
The current Unix epoch time in milliseconds\. This operation uses this timestamp to resolve concurrent updates to the key\. If the key in the component configuration has a greater timestamp than the timestamp in the request, then the request fails\.

`valueToMerge` \(Python: `value_to_merge`\)  
The configuration object to merge at the location that you specify in `keyPath`\. For more information, see [Update component configurations](update-component-configurations.md)\.

### Response<a name="ipc-operation-updateconfiguration-response"></a>

This operation doesn't provide any information in its response\.

## SubscribeToConfigurationUpdate<a name="ipc-operation-subscribetoconfigurationupdate"></a>

Subscribe to receive notifications when a component's configuration updates\. When you subscribe to a key, you receive a notification when any child of that key updates\.

<a name="ipc-subscribe-operation-note"></a>This operation is a subscription operation where you subscribe to a stream of event messages\. To use this operation, define a stream response handler with functions that handle event messages, errors, and stream closure\. For more information, see [Subscribe to IPC event streams](interprocess-communication.md#ipc-subscribe-operations)\.

**Event message type:** `ConfigurationUpdateEvents`

### Request<a name="ipc-operation-subscribetoconfigurationupdate-request"></a>

This operation's request has the following parameters:

`componentName` \(Python: `component_name`\)  <a name="ipc-configuration-request-component-name"></a>
\(Optional\) The name of the component\.  
Defaults to the name of the component that makes the request\.

`keyPath` \(Python: `key_path`\)  
The key path to the configuration value for which to subscribe\. Specify a list where each entry is the key for a single level in the configuration object\. For example, specify `["mqtt", "port"]` to get the value of `port` in the following configuration\.  

```
{
  "mqtt": {
    "port": 443
  }
}
```

### Response<a name="ipc-operation-subscribetoconfigurationupdate-response"></a>

This operation's response has the following information:

`messages`  
The stream of notification messages\. This object, `ConfigurationUpdateEvents`, contains the following information:    
`configurationUpdateEvent` \(Python: `configuration_update_event`\)  
The configuration update event\. This object, `ConfigurationUpdateEvent`, contains the following information:    
`componentName` \(Python: `component_name`\)  <a name="ipc-configuration-response-component-name"></a>
The name of the component\.  
`keyPath` \(Python: `key_path`\)  
The key path to the configuration value that updated\.

## SubscribeToValidateConfigurationUpdates<a name="ipc-operation-subscribetovalidateconfigurationupdates"></a>

Subscribe to receive notifications before this component's configuration updates\. This lets components validate updates to their own configuration\. Use the [SendConfigurationValidityReport](#ipc-operation-sendconfigurationvalidityreport) operation to tell the nucleus whether or not the configuration is valid\.

**Important**  
Local deployments don't notify components of updates\.

<a name="ipc-subscribe-operation-note"></a>This operation is a subscription operation where you subscribe to a stream of event messages\. To use this operation, define a stream response handler with functions that handle event messages, errors, and stream closure\. For more information, see [Subscribe to IPC event streams](interprocess-communication.md#ipc-subscribe-operations)\.

**Event message type:** `ValidateConfigurationUpdateEvents`

### Request<a name="ipc-operation-subscribetovalidateconfigurationupdates-request"></a>

This operation's request doesn't have any parameters\.

### Response<a name="ipc-operation-subscribetovalidateconfigurationupdates-response"></a>

This operation's response has the following information:

`messages`  
The stream of notification messages\. This object, `ValidateConfigurationUpdateEvents`, contains the following information:    
`validateConfigurationUpdateEvent` \(Python: `validate_configuration_update_event`\)  
The configuration update event\. This object, `ValidateConfigurationUpdateEvent`, contains the following information:    
`deploymentId` \(Python: `deployment_id`\)  
The ID of the AWS IoT Greengrass deployment that updates the component\.  
`configuration`  
The object that contains the new configuration\.

## SendConfigurationValidityReport<a name="ipc-operation-sendconfigurationvalidityreport"></a>

Tell the nucleus whether or not a configuration update to this component is valid\. The deployment fails if you tell the nucleus that the new configuration isn't valid\. Use the [SubscribeToValidateConfigurationUpdates](#ipc-operation-subscribetovalidateconfigurationupdates) operation to subscribe to validate configuration updates\.

If a component doesn't respond to a validate configuration update notification, the nucleus waits the amount of time that you specify in the deployment's configuration validation policy\. After that timeout, the nucleus proceeds with the deployment\. The default component validation timeout is 20 seconds\. For more information, see [Create deployments](create-deployments.md) and the [ConfigurationValidationPolicy](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ConfigurationValidationPolicy.html) object that you can provide when you call the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation\.

### Request<a name="ipc-operation-sendconfigurationvalidityreport-request"></a>

This operation's request has the following parameters:

`configurationValidityReport` \(Python: `configuration_validity_report`\)  
The report that tells the nucleus whether or not the configuration update is valid\. This object, `ConfigurationValidityReport`, contains the following information:    
`status`  
The validity status\. This enum, `ConfigurationValidityStatus`, has the following values:  
+ `ACCEPTED` – The configuration is valid and the nucleus can apply it to this component\.
+ `REJECTED` – The configuration isn't valid and the deployment fails\.  
`deploymentId` \(Python: `deployment_id`\)  
The ID of the AWS IoT Greengrass deployment that requested the configuration update\.  
`message`  
\(Optional\) A message that reports why the configuration isn't valid\.

### Response<a name="ipc-operation-sendconfigurationvalidityreport-response"></a>

This operation doesn't provide any information in its response\.
# Import a Lambda function as a component \(console\)<a name="import-lambda-function-console"></a>

When you use the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) to create a Lambda function component, you import an existing AWS Lambda function and then configure it to create a component that runs on your Greengrass device\.

Before you begin, review the [requirements]() to run Lambda functions on Greengrass devices\.

**Topics**
+ [Step 1: Choose a Lambda function to import](#import-lambda-console-choose-function)
+ [Step 2: Configure Lambda function parameters](#import-lambda-console-configure-function-parameters)
+ [Step 3: \(Optional\) Specify supported platforms for the Lambda function](#import-lambda-console-configure-platforms)
+ [Step 4: \(Optional\) Specify component dependencies for the Lambda function](#import-lambda-console-configure-dependencies)
+ [Step 5: \(Optional\) Run the Lambda function in a container](#import-lambda-console-run-isolated)
+ [Step 6: Create the Lambda function component](#import-lambda-console-create-deploy)

## Step 1: Choose a Lambda function to import<a name="import-lambda-console-choose-function"></a>

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

1. On the **Components** page, choose **Create component**\.

1. On the **Create component** page, under **Component information**, choose **Import Lambda function**\.

1. In **Lambda function**, search for and choose the Lambda function that you want to import\. 

   AWS IoT Greengrass creates the component with the name of the Lambda function\.

1. In **Lambda function version**, choose the version to import\. You can't choose Lambda aliases like `$LATEST`\.

   AWS IoT Greengrass creates the component with the version of the Lambda function as a valid semantic version\. For example, if your function version is `3`, the component version becomes `3.0.0`\.

## Step 2: Configure Lambda function parameters<a name="import-lambda-console-configure-function-parameters"></a>

On the **Create component** page, under **Lambda function configuration**, configure the following parameters to use to run the Lambda function\.

1. \(Optional\) Add the list of event sources to which the Lambda function subscribes for work messages\. The Lambda function is called when it receives a message from an event source\. You can subscribe this function to local publish/subscribe messages and AWS IoT Core MQTT messages\.

   Under **Event sources**, do the following to add an event source:

   1. For each event source that you add, specify the following options: 
      + **MQTT topic** – The name of the topic to subscribe for messages\.
      + **Type** – The type of event source\.

   1. To add another event source, choose **Add event source** and repeat the previous step\. To remove an event source, choose **Remove** next to the event source that you want to remove\.

1. For **Timeout \(seconds\)**, enter the maximum amount of time in seconds that a non\-pinned Lambda function can run before it times out\. The default is 3 seconds\.

1. For **Pinned**, choose whether the Lambda function component is pinned\. The default is **True**\.<a name="lambda-function-lifecycle-type"></a>
   + A pinned \(or long\-lived\) Lambda function starts when AWS IoT Greengrass starts and keeps running in its own container\.
   + A non\-pinned \(or on\-demand\) Lambda function starts only when it receives a work item and exits after it remains idle for a specified maximum idle time\. If the function has multiple work items, the AWS IoT Greengrass Core software creates multiple instances of the function\.

1. \(Optional\) Under **Additional parameters**, set the following Lambda function parameters\.
   + **Status timeout \(seconds\)** – The interval in seconds at which the Lambda function component sends status updates to the Lambda manager component\. This parameter applies only to pinned functions\. The default is 60 seconds\.
   + **Maximum queue size** – The maximum size of the message queue for the Lambda function component\. The AWS IoT Greengrass Core software stores messages in a FIFO \(first\-in, first\-out\) queue until it can run the Lambda function to consume each message\. The default is 1,000 messages\.
   + **Maximum number of instances** – The maximum number of instances that a non\-pinned Lambda function can run at the same time\. The default is 100 instances\.
   + **Maximum idle time \(seconds\)** – The maximum amount of time in seconds that a non\-pinned Lambda function can idle before the AWS IoT Greengrass Core software stops its process\. The default is 60 seconds\.
   + **Encoding type** – The type of payload that the Lambda function supports\. Choose from the following options:
     + **JSON**
     + **Binary**

     The default is JSON\.

1. \(Optional\) Specify the list of command line arguments to pass to the Lambda function when it runs\. 

   1. Under **Additional parameters, Process arguments**, choose **Add argument**\.

   1. For each argument that you add, enter the argument that you want to pass to the function\.

   1. To remove an argument, choose **Remove** next to the argument that you want to remove\.

1. \(Optional\) Specify the environment variables that are available to the Lambda function when it runs\. Environment variables enable you to store and update configuration settings without the need to change function code\.

   1. Under **Additional parameters, Environment variables**, choose **Add environment variable**\.

   1. For each environment variable that you add, specify the following options:
      + **Key** – The variable name\.
      + **Value** – The default value for this variable\.

   1. To remove an environment variable, choose **Remove** next to the environment variable that you want to remove\.

## Step 3: \(Optional\) Specify supported platforms for the Lambda function<a name="import-lambda-console-configure-platforms"></a>

All core devices have attributes for operating system and architecture\. When you deploy the Lambda function component, the AWS IoT Greengrass Core software compares the platform values that you specify with the platform attributes on the core device to determine whether the Lambda function is supported on that device\. 

**Note**  
You can also specify custom platform attributes when you deploy the Greengrass nucleus component to a core device\. For more information, see the [platform overrides parameter](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-platform-overrides) of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. 

Under **Lambda function configuration, Additional parameters, Platforms**, do the following to specify the platforms that this Lambda function supports\.

1. For each platform, specify the following options: 
   + **Operating system** – The name of the operating system for the platform\. Currently, the only supported value is `linux`\.
   + **Architecture** – The processor architecture for the platform\. Supported values are:
     + `amd64`
     + `arm`
     + `aarch64`
     + `x86`

1. To add another platform, choose **Add platform** and repeat the previous step\. To remove a supported platform, choose **Remove** next to the platform that you want to remove\.

## Step 4: \(Optional\) Specify component dependencies for the Lambda function<a name="import-lambda-console-configure-dependencies"></a>

Component dependencies identify additional AWS\-provided components or custom components that your function uses\. When you deploy the Lambda function component, the deployment includes these dependencies for your function to run\.

**Important**  <a name="import-v1-lambda-note"></a>
To import a Lambda function that you created to run on AWS IoT Greengrass V1, you must define individual component dependencies for the features that your function uses, such as subscriptions, secrets, and stream manager\. Define these components as [hard dependencies](component-recipe-reference.md) so that your Lambda function component restarts if the dependency changes state\. For more information, see [Run V1 Lambda functions](move-from-v1.md#run-v1-lambda-functions)\.

Under **Lambda function configuration, Additional parameters, Component dependencies**, complete the following steps to specify the component dependencies for your Lambda function\.

1.  Choose **Add dependency**\.

1. For each component dependency that you add, specify the following options: 
   + **Component name** – The component name\. For example, enter **aws\.greengrass\.StreamManager** to include the [stream manager component](stream-manager-component.md)\.
   + **Version requirement** – The semantic version constraint that identifies the compatible versions of this component dependency\. You can specify a single version or a range of versions\. For example, enter **^1\.0\.0** to specify that this Lambda function depends on any version in the first major version of the stream manager component\. For more information about semantic version constraints, see the [npm semver calculator](https://semver.npmjs.com/)\. 
   + **Type** – The type of dependency\. Choose from the following options:
     + **Hard** – The Lambda function component restarts if the dependency changes state\. This is the default selection\.
     + **Soft** – The Lambda function component doesn't restart if the dependency changes state\.

1. To remove a component dependency, choose **Remove** next to the component dependency

## Step 5: \(Optional\) Run the Lambda function in a container<a name="import-lambda-console-run-isolated"></a>

By default, Lambda functions run in an isolated runtime environment inside the AWS IoT Greengrass Core software\. You can also choose to run the Lambda function as a process without any isolation \(that is, in **No container** mode\)\. 

Under **Linux process configuration**, for **Isolation mode**, choose from the following options to select the containerization for your Lambda function:
+ **Greengrass container** – The Lambda function runs in a container\. This is the default selection\.
+ **No container** – The Lambda function runs as a process without any isolation\.

If you run the Lambda function in a container, complete the following steps to configure the process configuration for the Lambda function\. 

1. Configure the amount of memory and the system resources, such as volumes and devices, to make available to the container\.

   Under **Container parameters**, do the following\.

   1. For **Memory size**, enter the memory size that you want to allocate to the container\. You can specify the memory size in **MB** or **kB**\.

   1. For **Read\-only sys folder**, choose whether or not the container can read information from the device's `/sys` folder\. The default is **False**\.

1. \(Optional\) Configure the local volumes that the containerized Lambda function can access\. When you define a volume, the AWS IoT Greengrass Core software mounts the source files to the destination inside the container\. 

   1. Under **Volumes**, choose **Add volume**\. 

   1. For each volume that you add, specify the following options:
      + **Physical volume** – The path to the source folder on the core device\.
      + **Logical volume** – The path to the destination folder in the container\.
      + **Permission** – \(Optional\) The permission to access the source folder from the container\. Choose from the following options:
        + **Read\-only** – The Lambda function has read\-only access to the source folder\. This is the default selection\.
        + **Read\-write** – The Lambda function has read/write access to the source folder\.
      + **Add group owner** – \(Optional\) Whether or not to add the system group that runs the Lambda function component as an owner of the source folder\. The default is **False**\.

   1. To remove a volume, choose **Remove** next to the volume that you want to remove\.

1. \(Optional\) Configure the local system devices that the containerized Lambda function can access\.

   1. Under **Devices**, choose **Add device**\. 

   1. For each device that you add, specify the following options:
      + **Mount path** – The path to the system device on the core device\.
      + **Permission** – \(Optional\) The permission to access the system device from the container\. Choose from the following options:
        + **Read\-only** – The Lambda function has read\-only access to the system device\. This is the default selection\.
        + **Read\-write** – The Lambda function has read/write access to the source folder\.
      + **Add group owner** – \(Optional\) Whether or not to add the system group that runs the Lambda function component as an owner of the system device\. The default is **False**\.

## Step 6: Create the Lambda function component<a name="import-lambda-console-create-deploy"></a>

After you configure settings for your Lambda function component, choose **Create** to finish creating the new component\. 

To run the Lambda function on your core device, you can then deploy the new component to your core devices\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.
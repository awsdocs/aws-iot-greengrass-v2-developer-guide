# Manage AWS IoT Greengrass components<a name="manage-components"></a>

AWS IoT Greengrass *components* are software modules that you deploy to Greengrass core devices\. Components can represent applications, runtime installers, libraries, or any code that you would run on a device\. You can define components that depend on other components\. For example, you might define a component that installs Python, and then define that component as a dependency of your components that run Python applications\. When you deploy your components to your fleets of devices, Greengrass deploys only the software modules that your devices require\.

You can develop and test components on your Greengrass core device\. This lets you create and iterate your AWS IoT Greengrass software without interaction with the AWS Cloud\. When you finish a version of your component, you can upload it to AWS IoT Greengrass in the cloud, so you and your team can deploy the component to other devices in your fleet\. For more information about how to deploy components, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

Every component is composed of a *recipe* and *artifacts*\.
+ <a name="component-recipe-definition"></a>**Recipes**

  Every component contains a recipe file, which defines its metadata\. The recipe also specifies the component's configuration parameters, component dependencies, lifecycle, and platform compatibility\. The component lifecycle defines the commands that install, run, and shut down the component\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.

  You can define recipes in [JSON](https://en.wikipedia.org/wiki/JSON) or [YAML](https://en.wikipedia.org/wiki/YAML) format\.
+ <a name="component-artifacts-definition"></a>**Artifacts**

  Components can have any number of artifacts, which are component binaries\. Artifacts can include scripts, compiled code, static resources, and any other files that a component consumes\. Components can also consume artifacts from component dependencies\.

AWS IoT Greengrass provides pre\-built components that you can use in your applications and deploy to your devices\. For example, you can use the stream manager component to upload data to various AWS services, or you can use the CloudWatch metrics component to publish custom metrics to Amazon CloudWatch\. For more information, see [AWS\-provided components](public-components.md)\.

The AWS IoT Greengrass Core software runs components as the system user and group, such as `ggc_user` and `ggc_group`, that you configure on the core device\. This means that components have the permissions of that system user\. If you use a system user without a home directory, then components can't use run commands or code that use a home directory\. This means that you can't use the `pip install some-library --user` command to install Python packages for example\. If you followed the [getting started tutorial](getting-started.md) to set up your core device, then your system user doesn't have a home directory\. For more information about how to configure the user and group that run components, see [Configure the user and group that run components](configure-greengrass-core-v2.md#configure-component-user)\.

**Note**  <a name="semver-note"></a>
<a name="semver-para"></a>AWS IoT Greengrass uses semantic versions for components\. Semantic versions follow a *major*\.*minor*\.*patch* number system\. For example, version `1.0.0` represents the first major release for a component\. For more information, see the [semantic version specification](https://semver.org/)\.

**Topics**
+ [Component lifecycle](#component-lifecycle)
+ [Component types](#component-types)
+ [AWS\-provided components](public-components.md)
+ [Create custom AWS IoT Greengrass components](create-components.md)
+ [Upload components to deploy to your core devices](upload-components.md)
+ [Interact with AWS services](interact-with-aws-services.md)
+ [Run a Docker container](run-docker-container.md)
+ [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)
+ [Component environment variable reference](component-environment-variables.md)

## Component lifecycle<a name="component-lifecycle"></a>

The *component lifecycle* defines the stages that the AWS IoT Greengrass Core software uses to install and run components\. Each stage defines a script and other information that specifies how the component behaves\. For example, when you install a component, the AWS IoT Greengrass Core software runs the `Install` lifecycle script for that component\. Components on core devices have the following lifecycle states:
+ `NEW` – The component's recipe and artifacts are loaded on the core device, but the component isn't installed\. After a component enters this state, it runs its [bootstrap](component-recipe-reference.md#bootstrap-lifecycle-definition) and [install scripts](component-recipe-reference.md#install-lifecycle-definition)\.
+ `INSTALLED` – The component is installed on the core device\. The component enters this state after it runs its [install script](component-recipe-reference.md#install-lifecycle-definition)\.
+ `STARTING` – The component is starting on the core device\. The component enters this state when it runs its [startup script](component-recipe-reference.md#startup-lifecycle-definition)\. If the startup succeeds, the component enters the `RUNNING` state\.
+ `RUNNING` – The component is running on the core device\. The component enters this state when it runs its [run script](component-recipe-reference.md#run-lifecycle-definition) or when it has active background processes from its startup script\.
+ `FINISHED` – The component ran successfully and is no longer running\.
+ `STOPPING` – The component is stopping\. The component enters this state when it runs its [shutdown script](component-recipe-reference.md#shutdown-lifecycle-definition)\.
+ `ERRORED` – The component encountered an error\. When the component enters this state, it runs its [recover script](component-recipe-reference.md#recover-lifecycle-definition)\. Then, the component restarts to try to return to normal use\. If the component enters the `ERRORED` state three times without a successful run, the component becomes `BROKEN`\.
+ `BROKEN` – The component encountered errors multiple times and can't recover\. You must deploy the component again to fix it\.

## Component types<a name="component-types"></a>

The *component type* specifies how the AWS IoT Greengrass Core software runs the component\. Components can have the following types:
+ **Nucleus** \(`aws.greengrass.nucleus`\)

  The Greengrass nucleus is the component that provides the minimum functionality of the AWS IoT Greengrass Core software\. For more information, see [Greengrass nucleus](greengrass-nucleus-component.md)\.
+ **Plugin** \(`aws.greengrass.plugin`\)

  The Greengrass nucleus runs a plugin component in the same Java Virtual Machine \(JVM\) as the nucleus\. The nucleus restarts when you install or restart a plugin component\. To install and run plugin components, you must configure the Greengrass nucleus to run as a system service\. For more information, see [Configure AWS IoT Greengrass as a system service](configure-greengrass-core-v2.md#configure-system-service)\.

  Several components that are provided by AWS are plugin components, which enables them to interface directly with the Greengrass nucleus\. Plugin components use the same log file as the Greengrass nucleus\. For more information, see [View AWS IoT Greengrass Core software logs](troubleshooting.md#view-greengrass-core-logs)\.
+ **Generic** \(`aws.greengrass.generic`\)

  The Greengrass nucleus runs a generic component's lifecycle scripts, if the component defines a lifecycle\.

  This type is the default type for custom components\.
+ **Lambda** \(`aws.greengrass.lambda`\)

  The Greengrass nucleus runs a Lambda function component using the [Lambda launcher component](lambda-launcher-component.md)\.

  When you create a component from a Lambda function, the component has this type\. For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\.

**Note**  <a name="recipe-component-type-recommendation"></a>
We don't recommend that you specify the component type in a recipe\. AWS IoT Greengrass sets the type for you when you create a component\.
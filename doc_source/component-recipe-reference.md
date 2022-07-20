# AWS IoT Greengrass component recipe reference<a name="component-recipe-reference"></a>

The component recipe is a file that defines a component's details, dependencies, artifacts, and lifecycles\. The component *lifecycle* specifies the commands to run to install, run, and shut down the component, for example\. The AWS IoT Greengrass core uses the lifecycles that you define in the recipe to install and run components\. The AWS IoT Greengrass service uses the recipe to identify the dependencies and artifacts to deploy to your core devices when you deploy the component\.

In the recipe, you can define unique dependencies and lifecycles for each platform that a component supports\. You can use this capability to deploy a component to devices with multiple platforms that have different requirements\. You can also use this to prevent AWS IoT Greengrass from installing a component on devices that don't support it\.

Each recipe contains a list of *manifests*\. Each manifest specifies a set of platform requirements and the lifecycle and artifacts to use for core devices whose platform meets those requirements\. The core device uses the first manifest with platform requirements that the device meets\. Specify a manifest without any platform requirements to match any core device\.

You can also specify a global lifecycle that isn't in a manifest\. In the global lifecycle, you can use *selection keys* that identify sub\-sections of the lifecycle\. Then, you can specify these selection keys within a manifest to use those sections of the global lifecycle in addition to the manifest's lifecycle\. The core device uses the manifest's selection keys only if the manifest doesn't define a lifecycle\. You can use the `all` selection in a manifest to match sections of the global lifecycle without selection keys\.

After the AWS IoT Greengrass Core software selects a manifest that matches the core device, it does the following to identify the lifecycle steps to use:
+ If the selected manifest defines a lifecycle, the core device uses that lifecycle\.
+ If the selected manifest doesn't define a lifecycle, the core device uses the global lifecycle\. The core device does the following to identify which sections of the global lifecycle to use:
  + If the manifest defines selection keys, the core device uses the sections of the global lifecycle that contain the manifest's selection keys\.
  + If the manifest doesn't define selection keys, the core device uses the sections of the global lifecycle that don't have selection keys\. This behavior is equivalent to a manifest that defines the `all` selection\.

**Important**  <a name="recipe-core-device-manifest-requirement"></a>
A core device must match least one manifest's platform requirements to install the component\. If no manifest matches the core device, then the AWS IoT Greengrass Core software doesn't install the component and the deployment fails\.

You can define recipes in [JSON](https://en.wikipedia.org/wiki/JSON) or [YAML](https://en.wikipedia.org/wiki/YAML) format\. The recipe examples section includes recipes in each format\.

**Topics**
+ [Recipe format](#recipe-format)
+ [Recipe variables](#recipe-variables)
+ [Recipe examples](#recipe-examples)

## Recipe format<a name="recipe-format"></a>

When you define a recipe for a component, you specify the following information in the recipe document\. The same structure applies to recipes in YAML and JSON formats\.

`RecipeFormatVersion`  
The template version for the recipe\. Choose the following option:  
+ `2020-01-25`

`ComponentName`  
The name of the component that this recipe defines\. The component name must be unique in your AWS account in each Region\.  
**Tips**  
+ Use inverse domain name format to avoid name collision within your company\. For example, if your company owns `example.com` and you work on a solar energy project, you can name your Hello World component `com.example.solar.HelloWorld`\. This helps avoid component name collisions within your company\.
+ Avoid the `aws.greengrass` prefix in your component names\. AWS IoT Greengrass uses this prefix for the [public components](public-components.md) that it provides\. If you choose the same name as a public component, your component replaces that component\. Then, AWS IoT Greengrass provides your component instead of the public component when it deploys components with a dependency on that public component\. This feature enables you to override the behavior of public components, but it can also break other components if you don't intend to override a public component\.

`ComponentVersion`  
The version of the component\.  
<a name="semver-para"></a>AWS IoT Greengrass uses semantic versions for components\. Semantic versions follow a *major*\.*minor*\.*patch* number system\. For example, version `1.0.0` represents the first major release for a component\. For more information, see the [semantic version specification](https://semver.org/)\.

`ComponentDescription`  
\(Optional\) The description of the component\.

`ComponentPublisher`  
The publisher or author of the component\.

`ComponentConfiguration`  
\(Optional\) An object that defines the configuration or parameters for the component\. You define the default configuration, and then when you deploy the component, you can specify the configuration object to provide to the component\. Component configuration supports nested parameters and structures\. This object contains the following information:    
`DefaultConfiguration`  
An object that defines the default configuration for the component\. You define the structure of this object\.  
<a name="configuration-value-type-note"></a>AWS IoT Greengrass uses JSON for configuration values\. JSON specifies a number type but doesn't differentiate between integers and floats\. As a result, configuration values might convert to floats in AWS IoT Greengrass\. To ensure that your component uses the correct data type, we recommend that you define numeric configuration values as strings\. Then, have your component parse them as integers or floats\. This ensures that your configuration values have the same type in the configuration and on your core device\.

`ComponentDependencies`  <a name="recipe-reference-component-dependencies"></a>
\(Optional\) A dictionary of objects that each define a component dependency for the component\. The key for each object identifies the name of the component dependency\. AWS IoT Greengrass installs component dependencies when the component installs\. AWS IoT Greengrass waits for dependencies to start before it starts the component\. Each object contains the following information:    
`VersionRequirement`  
The npm\-style semantic version constraint that defines the compatible component versions for this dependency\. You can specify a version or a range of versions\. For more information, see the [npm semantic version calculator](https://semver.npmjs.com/)\.  
`DependencyType`  
\(Optional\) The type of this dependency\. Choose from the following options\.  
+ `SOFT` – The component doesn't restart if the dependency changes state\.
+ `HARD` – The component restarts if the dependency changes state\.
Defaults to `HARD`\.

`ComponentType`  
\(Optional\) The type of component\.  
We don't recommend that you specify the component type in a recipe\. AWS IoT Greengrass sets the type for you when you create a component\.
The type can be one the following types:  
+ `aws.greengrass.generic` – The component runs commands or provides artifacts\.
+ `aws.greengrass.lambda` – The component runs a Lambda function using the [Lambda launcher component](lambda-launcher-component.md)\. The `ComponentSource` parameter specifies the ARN of the Lambda function that this component runs\.

  We don't recommend that you use this option, because it's set by AWS IoT Greengrass when you create a component from a Lambda function\. For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\.
+ `aws.greengrass.plugin` – The component runs in the same Java Virtual Machine \(JVM\) as the Greengrass nucleus\. If you deploy or restart a plugin component, the Greengrass nucleus restarts\.

  Plugin components use the same log file as the Greengrass nucleus\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

  We don't recommend that you use this option in component recipes, because it's intended for AWS\-provided components written in Java that directly interface with the Greengrass nucleus\. For more information about which public components are plugins, see [AWS\-provided components](public-components.md)\.
+ `aws.greengrass.nucleus` – The nucleus component\. For more information, see [Greengrass nucleus](greengrass-nucleus-component.md)\.

  We don't recommend that you use this option in component recipes\. It is intended for the Greengrass nucleus component, which provides the minimum functionality of the AWS IoT Greengrass Core software\.
Defaults to `aws.greengrass.generic` when you create a component from a recipe, or `aws.greengrass.lambda` when you create a component from a Lambda function\.  
For more information, see [Component types](develop-greengrass-components.md#component-types)\.

`ComponentSource`  
\(Optional\) The ARN of the Lambda function that a component runs\.  
We don't recommend that you specify the component source in a recipe\. AWS IoT Greengrass sets this parameter for you when you create a component from a Lambda function\. For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\.

  `Manifests`   
A list of objects that each define the component's lifecycle, parameters, and requirements for a platform\. If a core device matches multiple manifests' platform requirements, AWS IoT Greengrass uses the first manifest that the core device matches\. To ensure that core devices use the correct manifest, define the manifests with stricter platform requirements first\. A manifest that applies to all platforms must be the last manifest in the list\.  
A core device must match least one manifest's platform requirements to install the component\. If no manifest matches the core device, then the AWS IoT Greengrass Core software doesn't install the component and the deployment fails\.
Each object contains the following information:    
`Name`  
\(Optional\) A friendly name for the platform that this manifest defines\.  
If you omit this parameter, AWS IoT Greengrass creates a name from the platform `os` and `architecture`\.  
  `Platform`   
\(Optional\) An object that defines the platform to which this manifest applies\. Omit this parameter to define a manifest that applies to all platforms\.  
This object specifies key\-value pairs about the platform on which a core device runs\. When you deploy this component, the AWS IoT Greengrass Core software compares these key\-value pairs with the platform attributes on the core device\. The AWS IoT Greengrass Core software always defines `os` and `architecture`, and it might define additional attributes\. You can specify custom platform attributes for a core device when you deploy the Greengrass nucleus component\. For more information, see the [platform overrides parameter](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-platform-overrides) of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.  
For each key\-value pair, you can specify one of the following values:  
+ An exact value, such as `linux` or `windows`\. Exact values must start with a letter or a number\.
+ `*`, which matches any value\. This also matches when a value isn't present\.
+ A Java\-style regular expression, such as `/windows|linux/`\. The regular expression must start and end with a slash character \(`/`\)\. For example, the regular expression `/.+/` matches any non\-blank value\.
This object contains the following information:    
`os`  
\(Optional\) The name of the operating system for the platform that this manifest supports\. Common platforms include the following values:  
+ `linux`
+ `windows`
+ `darwin` \(macOS\)  
`architecture`  
\(Optional\) The processor architecture for the platform that this manifest supports\. Common architectures include the following values:  
+ `amd64`
+ `arm`
+ `aarch64`
+ `x86`  
`key`  
\(Optional\) A platform attribute that you define for this manifest\. Replace *key* with the name of the platform attribute\. The AWS IoT Greengrass Core software matches this platform attribute with the key\-value pairs that you specify in the Greengrass nucleus component configuration\. For more information, see the [platform overrides parameter](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-platform-overrides) of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.  
Use inverse domain name format to avoid name collision within your company\. For example, if your company owns `example.com` and you work on a radio project, you can name a custom platform attribute `com.example.radio.RadioModule`\. This helps avoid platform attribute name collisions within your company\.
For example, you might define a platform attribute, `com.example.radio.RadioModule`, to specify a different manifest based on which radio module is available on a core device\. Each manifest can include different artifacts that apply to different hardware configurations, so that you deploy the minimal set of software to the core device\.  
  `Lifecycle`   
An object or string that defines how to install and run the component on the platform that this manifest defines\. You can also define a [global lifecycle](#global-lifecycle-definition) that applies to all platforms\. The core device uses the global lifecycle only if the manifest to use doesn't specify a lifecycle\.  
You define this lifecycle within a manifest\. The lifecycle steps that you specify here apply to only the platform that this manifest defines\. You can also define a [global lifecycle](#global-lifecycle-definition) that applies to all platforms\.
This object or string contains the following information:    
  `Setenv`   
\(Optional\) A dictionary of environment variables to provide to all lifecycle scripts\. You can override these environment variables with `Setenv` in each lifecycle script\.  
  `Install`   
\(Optional\) An object or string that defines the script to run when the component installs\. The AWS IoT Greengrass Core software also runs this lifecycle step each time the software launches\.  
If the `Install` script exits with a success code, the component enters the `INSTALLED` state\.  
This object or string contains the following information:    
`Script`  <a name="recipe-lifecycle-script"></a>
The script to run\.  
`RequiresPrivilege`  <a name="recipe-lifecycle-requiresprivilege"></a>
\(Optional\) You can run the script with root privileges\. If you set this option to `true`, then the AWS IoT Greengrass Core software runs this lifecycle script as root instead of as the system user that you configure to run this component\. Defaults to `false`\.  
`Skipif`  <a name="recipe-lifecycle-skipif"></a>
\(Optional\) The check to determine whether or not to run the script\. You can define to check if an executable is on the path or if a file exists\. If the output is true, then the AWS IoT Greengrass Core software skips the step\. Choose from the following checks:  
+ `onpath runnable` – Check if a runnable is on the system path\. For example, use **onpath python3** to skip this lifecycle step if Python 3 is available\.
+ `exists file` – Check if a file exists\. For example, use **exists /tmp/my\-configuration\.db** to skip this lifecycle step if `/tmp/my-configuration.db` is present\.  
`Timeout`  <a name="recipe-lifecycle-timeout"></a>
\(Optional\) The maximum amount of time in seconds that the script can run before the AWS IoT Greengrass Core software terminates the process\.  
Default: 120 seconds  
`Setenv`  <a name="recipe-lifecycle-environment"></a>
\(Optional\) The dictionary of environment variables to provide to the script\. These environment variables override the variables that you provide in `Lifecycle.Setenv`\.  
  `Run`   
\(Optional\) An object or string that defines the script to run when the component starts\.  
The component enters the `RUNNING` state when this lifecycle step runs\. If the `Run` script exits with a success code, the component enters the `FINISHED` state\.  
Components that depend on this component start when this lifecycle step runs\. To run a background process, such as a service that dependent components use, use the `Startup` lifecycle step instead\.  
You can define only one `Startup` or `Run` lifecycle\.
This object or string contains the following information:    
`Script`  <a name="recipe-lifecycle-script"></a>
The script to run\.  
`RequiresPrivilege`  <a name="recipe-lifecycle-requiresprivilege"></a>
\(Optional\) You can run the script with root privileges\. If you set this option to `true`, then the AWS IoT Greengrass Core software runs this lifecycle script as root instead of as the system user that you configure to run this component\. Defaults to `false`\.  
`Skipif`  <a name="recipe-lifecycle-skipif"></a>
\(Optional\) The check to determine whether or not to run the script\. You can define to check if an executable is on the path or if a file exists\. If the output is true, then the AWS IoT Greengrass Core software skips the step\. Choose from the following checks:  
+ `onpath runnable` – Check if a runnable is on the system path\. For example, use **onpath python3** to skip this lifecycle step if Python 3 is available\.
+ `exists file` – Check if a file exists\. For example, use **exists /tmp/my\-configuration\.db** to skip this lifecycle step if `/tmp/my-configuration.db` is present\.  
`Timeout`  <a name="recipe-lifecycle-timeout"></a>
\(Optional\) The maximum amount of time in seconds that the script can run before the AWS IoT Greengrass Core software terminates the process\.  
This lifecycle step doesn't timeout by default\. If you omit this timeout, the `Run` script runs until it exits\.  
`Setenv`  <a name="recipe-lifecycle-environment"></a>
\(Optional\) The dictionary of environment variables to provide to the script\. These environment variables override the variables that you provide in `Lifecycle.Setenv`\.  
  `Startup`   
\(Optional\) An object or string that defines the background process to run when the component starts\.  
Use `Startup` to run a command that must exit successfully before dependent components can start\. For example, you might define a `Startup` step that starts the MySQL process with `/etc/init.d/mysqld start`\.  
The component enters the `STARTING` state when this lifecycle step runs\. If the `Startup` script exits with a success code, the component enters the `RUNNING` state\. Then, dependent components can start\.  
You can define only one `Startup` or `Run` lifecycle\.
This object or string contains the following information:    
`Script`  <a name="recipe-lifecycle-script"></a>
The script to run\.  
`RequiresPrivilege`  <a name="recipe-lifecycle-requiresprivilege"></a>
\(Optional\) You can run the script with root privileges\. If you set this option to `true`, then the AWS IoT Greengrass Core software runs this lifecycle script as root instead of as the system user that you configure to run this component\. Defaults to `false`\.  
`Skipif`  <a name="recipe-lifecycle-skipif"></a>
\(Optional\) The check to determine whether or not to run the script\. You can define to check if an executable is on the path or if a file exists\. If the output is true, then the AWS IoT Greengrass Core software skips the step\. Choose from the following checks:  
+ `onpath runnable` – Check if a runnable is on the system path\. For example, use **onpath python3** to skip this lifecycle step if Python 3 is available\.
+ `exists file` – Check if a file exists\. For example, use **exists /tmp/my\-configuration\.db** to skip this lifecycle step if `/tmp/my-configuration.db` is present\.  
`Timeout`  <a name="recipe-lifecycle-timeout"></a>
\(Optional\) The maximum amount of time in seconds that the script can run before the AWS IoT Greengrass Core software terminates the process\.  
Default: 120 seconds  
`Setenv`  <a name="recipe-lifecycle-environment"></a>
\(Optional\) The dictionary of environment variables to provide to the script\. These environment variables override the variables that you provide in `Lifecycle.Setenv`\.  
  `Shutdown`   
\(Optional\) An object or string that defines the script to run when the component shuts down\.  
If you start a background process in `Startup`, use the `Shutdown` step to stop that process when the component shuts down\. For example, you might define a `Shutdown` step that stops the MySQL process with `/etc/init.d/mysqld stop`\.  
The component enters the `STOPPING` state when this lifecycle step runs\.  
This object or string contains the following information:    
`Script`  <a name="recipe-lifecycle-script"></a>
The script to run\.  
`RequiresPrivilege`  <a name="recipe-lifecycle-requiresprivilege"></a>
\(Optional\) You can run the script with root privileges\. If you set this option to `true`, then the AWS IoT Greengrass Core software runs this lifecycle script as root instead of as the system user that you configure to run this component\. Defaults to `false`\.  
`Skipif`  <a name="recipe-lifecycle-skipif"></a>
\(Optional\) The check to determine whether or not to run the script\. You can define to check if an executable is on the path or if a file exists\. If the output is true, then the AWS IoT Greengrass Core software skips the step\. Choose from the following checks:  
+ `onpath runnable` – Check if a runnable is on the system path\. For example, use **onpath python3** to skip this lifecycle step if Python 3 is available\.
+ `exists file` – Check if a file exists\. For example, use **exists /tmp/my\-configuration\.db** to skip this lifecycle step if `/tmp/my-configuration.db` is present\.  
`Timeout`  
\(Optional\) The maximum amount of time in seconds that the script can run before the AWS IoT Greengrass Core software terminates the process\.  
Default: 15 seconds\.  
`Setenv`  <a name="recipe-lifecycle-environment"></a>
\(Optional\) The dictionary of environment variables to provide to the script\. These environment variables override the variables that you provide in `Lifecycle.Setenv`\.  
  `Recover`   
\(Optional\) An object or string that defines the script to run when the component encounters an error\.  
This step runs when a component enters the `ERRORED` state\. If the component becomes `ERRORED` three times without successfully recovering, the component changes to the `BROKEN` state\. To fix a `BROKEN` component, you must deploy it again\.  
This object or string contains the following information:    
`Script`  <a name="recipe-lifecycle-script"></a>
The script to run\.  
`RequiresPrivilege`  <a name="recipe-lifecycle-requiresprivilege"></a>
\(Optional\) You can run the script with root privileges\. If you set this option to `true`, then the AWS IoT Greengrass Core software runs this lifecycle script as root instead of as the system user that you configure to run this component\. Defaults to `false`\.  
`Skipif`  <a name="recipe-lifecycle-skipif"></a>
\(Optional\) The check to determine whether or not to run the script\. You can define to check if an executable is on the path or if a file exists\. If the output is true, then the AWS IoT Greengrass Core software skips the step\. Choose from the following checks:  
+ `onpath runnable` – Check if a runnable is on the system path\. For example, use **onpath python3** to skip this lifecycle step if Python 3 is available\.
+ `exists file` – Check if a file exists\. For example, use **exists /tmp/my\-configuration\.db** to skip this lifecycle step if `/tmp/my-configuration.db` is present\.  
`Timeout`  
\(Optional\) The maximum amount of time in seconds that the script can run before the AWS IoT Greengrass Core software terminates the process\.  
Default: 60 seconds\.  
`Setenv`  <a name="recipe-lifecycle-environment"></a>
\(Optional\) The dictionary of environment variables to provide to the script\. These environment variables override the variables that you provide in `Lifecycle.Setenv`\.  
  `Bootstrap`   
\(Optional\) An object or string that defines a script that requires the AWS IoT Greengrass Core software or core device to restart\. This lets you develop a component that performs a restart after it installs operating system updates or runtime updates, for example\.  
To install updates or dependencies that don't require the AWS IoT Greengrass Core software or device to restart, use the [install lifecycle](#install-lifecycle-definition)\.
This lifecycle step runs before the install lifecycle step in the following cases when the AWS IoT Greengrass Core software deploys the component:  
+ The component deploys to the core device for the first time\.
+ The component version changes\.
+ The bootstrap script changes as the result of a component configuration update\.
After the AWS IoT Greengrass Core software completes the bootstrap step for all components that have a bootstrap step in a deployment, the software restarts\.  
You must configure the AWS IoT Greengrass Core software as a system service to restart the AWS IoT Greengrass Core software or the core device\. If you don't configure the AWS IoT Greengrass Core software as a system service, the software won't restart\. For more information, see [Configure the Greengrass nucleus as a system service](configure-greengrass-core-v2.md#configure-system-service)\.
This object or string contains the following information:    
`Script`  
The script to run\. The exit code of this script defines the restart instruction\. Use the following exit codes:  
+ `0` – Don't restart the AWS IoT Greengrass Core software or the core device\. The AWS IoT Greengrass Core software still restarts after all components bootstrap\.
+ `100` – Request to restart the AWS IoT Greengrass Core software\.
+ `101` – Request to restart the core device\.
Exit codes 100 to 199 are reserved for special behavior\. Other exit codes represent script errors\.  
`RequiresPrivilege`  <a name="recipe-lifecycle-requiresprivilege"></a>
\(Optional\) You can run the script with root privileges\. If you set this option to `true`, then the AWS IoT Greengrass Core software runs this lifecycle script as root instead of as the system user that you configure to run this component\. Defaults to `false`\.  
`Timeout`  <a name="recipe-lifecycle-timeout"></a>
\(Optional\) The maximum amount of time in seconds that the script can run before the AWS IoT Greengrass Core software terminates the process\.  
Default: 120 seconds  
`Setenv`  <a name="recipe-lifecycle-environment"></a>
\(Optional\) The dictionary of environment variables to provide to the script\. These environment variables override the variables that you provide in `Lifecycle.Setenv`\.  
  `Selections`   
\(Optional\) A list of selection keys that specify sections of the [global lifecycle](#global-lifecycle-definition) to run for this manifest\. In the global lifecycle, you can define lifecycle steps with selection keys at any level to select sub\-sections of the lifecycle\. Then, the core device uses those sections that match the selection keys in this manifest\. For more information, see the [global lifecycle examples](#global-lifecycle-definition)\.  
The core device uses the selections from the global lifecycle only if this manifest doesn't define a lifecycle\.
You can specify the `all` selection key to run sections of the global lifecycle that don't have selection keys\.  
  `Artifacts`   
\(Optional\) A list of objects that each define a binary artifact for the component on the platform that this manifest defines\. For example, you can define code or images as artifacts\.  
When the component deploys, the AWS IoT Greengrass Core software downloads the artifact to a folder on the core device\. You can also define artifacts as archive files that the software extracts after it downloads them\.  
You can use [recipe variables](#recipe-variables) to get the paths to the folders where the artifacts install on the core device\.  
+ Normal files – Use the [artifacts:path recipe variable](#component-recipe-artifacts-path) to get the path to the folder that contains the artifacts\. For example, specify `{artifacts:path}/my_script.py` in a recipe to get the path to an artifact that has the URI `s3://DOC-EXAMPLE-BUCKET/path/to/my_script.py`\.
+ Extracted archives – Use the [artifacts:decompressedPath recipe variable](#component-recipe-artifacts-decompressed-path) to get the path to the folder that contains the extracted archive artifacts\. The AWS IoT Greengrass Core software extracts each archive to a folder with the same name as the archive\. For example, specify `{artifacts:decompressedPath}/my_archive/my_script.py` in a recipe to get the path to `my_script.py` in the archive artifact that has the URI `s3://DOC-EXAMPLE-BUCKET/path/to/my_archive.zip`\.
When you develop a component with an archive artifact on a local core device, you might not have a URI for that artifact\. To test your component with an `Unarchive` option that extracts the artifact, specify a URI where the file name matches the name of your archive artifact file\. You can specify the URI where you expect to upload the archive artifact, or you can specify a new placeholder URI\. For example, to extract the `my_archive.zip` artifact during a local deployment, you can specify `s3://DOC-EXAMPLE-BUCKET/my_archive.zip`\.
Each object contains the following information:    
`URI`  
The URI of an artifact in an S3 bucket\. The AWS IoT Greengrass Core software fetches the artifact from this URI when the component installs, unless the artifact already exists on the device\. Each artifact must have a unique file name within each manifest\.  
`Unarchive`  
\(Optional\) The type of archive to unpack\. Choose from the following options:  
+ `NONE` – The file isn't an archive to unpack\. The AWS IoT Greengrass Core software installs the artifact to a folder on the core device\. You can use the [artifacts:path recipe variable](#component-recipe-artifacts-path) to get the path to this folder\.
+ `ZIP` – The file is a ZIP archive\. The AWS IoT Greengrass Core software extracts the archive to a folder with the same name as the archive\. You can use the [artifacts:decompressedPath recipe variable](#component-recipe-artifacts-decompressed-path) to get the path to the folder that contains this folder\.
Defaults to `NONE`\.  
  `Permission`   
\(Optional\) An object that defines the access permissions to set for this artifact file\. You can set the read permission and the execute permission\.  
You can't set the write permission, because the AWS IoT Greengrass Core software doesn't allow components to edit artifact files in the artifacts folder\. To edit an artifact file in a component, copy it to another location or publish and deploy a new artifact file\.
If you define an artifact as an archive to unpack, then the AWS IoT Greengrass Core software sets these access permissions on the files that it unpacks from the archive\. The AWS IoT Greengrass Core software sets the folder's access permissions to `ALL` for `Read` and `Execute`\. This allows components to view the unpacked files in the folder\. To set permissions on individual files from the archive, you can set the permissions in the [install lifecycle script](#install-lifecycle-definition)\.  
This object contains the following information:    
`Read`  
\(Optional\) The read permission to set for this artifact file\. To allow other components to access this artifact, such as components that depend on this component, specify `ALL`\. Choose from the following options:  
+ `NONE` – The file isn't readable\.
+ `OWNER` – The file is readable by the system user that you configure to run this component\.
+ `ALL` – The file is readable by all users\.
Defaults to `OWNER`\.  
`Execute`  
\(Optional\) The run permission to set for this artifact file\. The `Execute` permission implies the `Read` permission\. For example, if you specify `ALL` for `Execute`, then all users can read and run this artifact file\.  
Choose from the following options:  
+ `NONE` – The file isn't runnable\.
+ `OWNER` – The file is runnable by the system user that you configure to run the component\.
+ `ALL` – The file is runnable by all users\.
Defaults to `NONE`\.  
`Digest`  
\(Read\-only\) The cryptographic digest hash of the artifact\. When you create a component, AWS IoT Greengrass uses a hash algorithm to calculate a hash of the artifact file\. Then, when you deploy the component, the Greengrass nucleus calculates the hash of the downloaded artifact and compares the hash with this digest to verify the artifact before installation\. If the hash doesn't match the digest, the deployment fails\.  
If you set this parameter, AWS IoT Greengrass replaces the value that you set when you create the component\.  
`Algorithm`  
\(Read\-only\) The hash algorithm that AWS IoT Greengrass uses to calculate the digest hash of the artifact\.  
If you set this parameter, AWS IoT Greengrass replaces the value that you set when you create the component\.

  `Lifecycle`   
An object that defines how to install and run the component\. The core device uses the global lifecycle only if the [manifest](#manifest-definition) to use doesn't specify a lifecycle\.  
You define this lifecycle outside a manifest\. You can also define a [manifest lifecycle](#manifest-lifecycle-definition) that applies to the platforms that match that manifest\.
In the global lifecycle, you can specify lifecycles that run for certain [selection keys](#manifest-selections-definition) that you specify in each manifest\. Selection keys are strings that identify sections of the global lifecycle to run for each manifest\.  
The `all` selection key is the default on any section without a selection key\. This means that you can specify the `all` selection key in a manifest to run the sections of the global lifecycle without selection keys\. You don't need to specify the `all` selection key in the global lifecycle\.  
If a manifest doesn't define a lifecycle or selection keys, the core device defaults to use the `all` selection\. This means that in this case, the core device uses the sections of the global lifecycle that don't use selection keys\.  
This object contains the same information as the [manifest lifecycle](#manifest-lifecycle-definition), but you can specify selection keys at any level to select sub\-sections of the lifecycle\.  
We recommend that you use only lowercase letters for each selection key to avoid conflicts between selection keys and lifecycle keys\. Lifecycle keys start with a capital letter\.

**Example global lifecycle with top\-level selection keys**  

```
Lifecycle:
  key1:
    Install:
      Skipif: onpath executable | exists file
      Script: command1
  key2:
    Install:
      Script: command2
  all:
    Install:
      Script: command3
```

**Example global lifecycle with bottom\-level selection keys**  

```
Lifecycle:
  Install:
    Script:
      key1: command1
      key2: command2
      all: command3
```

**Example global lifecycle with multiple levels of selection keys**  

```
Lifecycle:
  key1:
    Install:
      Skipif: onpath executable | exists file
      Script: command1
  key2:
    Install:
      Script: command2
  all:
    Install:
      Script:
        key3: command3
        key4: command4
        all: command5
```

## Recipe variables<a name="recipe-variables"></a>

Recipe variables expose information from the current component and nucleus for you to use in your recipes\. For example, you can use a recipe variable to pass component configuration parameters to an application that you run in a lifecycle script\.

You can use recipe variables in the following sections of component recipes:
+ Lifecycle definitions\.
+ Component configuration definitions, if you use [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later\. You can also use recipes variables when you [deploy component configuration updates](update-component-configurations.md#merge-configuration-update-recipe-variables)\.

Recipe variables use `{recipe_variable}` syntax\. The curly braces indicate a recipe variable\.

AWS IoT Greengrass supports the following recipe variables:

`component_dependency_name:configuration:json_pointer`  
The value of a configuration parameter for the component that this recipe defines or for a component on which this component depends\.  
You can use this variable to provide a parameter to a script that you run in the component lifecycle\.  
AWS IoT Greengrass supports this recipe variable only in component lifecycle definitions\.
This recipe variable has the following inputs:  
+ <a name="recipe-variable-component-dependency-name"></a>`component_dependency_name` – \(Optional\) The name of the component dependency to query\. Omit this segment to query the component that this recipe defines\. You can specify only direct dependencies\.
+ `json_pointer` – The JSON pointer to the configuration value to evaluate\. JSON pointers start with a forward slash `/`\. To identify a value in a nested component configuration, use forward slashes \(`/`\) to separate the keys for each level in the configuration\. You can use a number as a key to specify an index in a list\. For more information, see the [JSON pointer specification](https://tools.ietf.org/html/rfc6901)\.

  AWS IoT Greengrass Core uses JSON pointers for recipes in YAML format\.
The JSON pointer can reference the following node types:  
+ A value node\. AWS IoT Greengrass Core replaces the recipe variable with the string representation of the value\. Null values convert to `null` as a string\.
+ An object node\. AWS IoT Greengrass Core replaces the recipe variable with the serialized JSON string representation of that object\.
+ No node\. AWS IoT Greengrass Core doesn't replace the recipe variable\.
For example, the `{configuration:/Message}` recipe variable retrieves the value of the `Message` key in the component configuration\. The `{com.example.MyComponentDependency:configuration:/server/port}` recipe variable retrieves the value of `port` in the `server` configuration object of a component dependency\.

  `component_dependency_name:artifacts:path`   
The root path of the artifacts for the component that this recipe defines or for a component on which this component depends\.  
When a component installs, AWS IoT Greengrass copies the component's artifacts to the folder that this variable exposes\. You can use this variable to identify the location of a script to run in the component lifecycle, for example\.  
<a name="recipe-variable-artifact-folder-permissions"></a>The folder at this path is read\-only\. To modify artifact files, copy the files to another location, such as the current working directory \(`$PWD` or `.`\)\. Then, modify the files there\.  
<a name="recipe-variable-component-dependency-artifact-file-permissions"></a>To read or run an artifact from a component dependency, that artifact's `Read` or `Execute` permission must be `ALL`\. For more information, see the [artifact permissions](#component-artifact-permission) that you define in the component recipe\.  
This recipe variable has the following inputs:  
+ <a name="recipe-variable-component-dependency-name"></a>`component_dependency_name` – \(Optional\) The name of the component dependency to query\. Omit this segment to query the component that this recipe defines\. You can specify only direct dependencies\.

  `component_dependency_name:artifacts:decompressedPath`   
The root path of the decompressed archive artifacts for the component that this recipe defines or for a component on which this component depends\.  
When a component installs, AWS IoT Greengrass unpacks the component's archive artifacts to the folder that this variable exposes\. You can use this variable to identify the location of a script to run in the component lifecycle, for example\.  
Each artifact unzips to a folder within the decompressed path, where the folder has the same name as the artifact minus its extension\. For example, a ZIP artifact named `models.zip` unpacks to the `{artifacts:decompressedPath}/models` folder\.  
<a name="recipe-variable-artifact-folder-permissions"></a>The folder at this path is read\-only\. To modify artifact files, copy the files to another location, such as the current working directory \(`$PWD` or `.`\)\. Then, modify the files there\.  
<a name="recipe-variable-component-dependency-artifact-file-permissions"></a>To read or run an artifact from a component dependency, that artifact's `Read` or `Execute` permission must be `ALL`\. For more information, see the [artifact permissions](#component-artifact-permission) that you define in the component recipe\.  
This recipe variable has the following inputs:  
+ <a name="recipe-variable-component-dependency-name"></a>`component_dependency_name` – \(Optional\) The name of the component dependency to query\. Omit this segment to query the component that this recipe defines\. You can specify only direct dependencies\.

  `component_dependency_name:work:path`   
This feature is available for v2\.0\.4 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.  
The work path for the component that this recipe defines or for a component on which this component depends\. The value of this recipe variable is equivalent to the output of the `$PWD` environment variable and the [pwd](https://en.wikipedia.org/wiki/Pwd) command when run from the context of the component\.  
You can use this recipe variable to share files between a component and a dependency\.  
The folder at this path is readable and writable by the component that this recipe defines and by other components that run as the same user and group\.  
This recipe variable has the following inputs:  
+ <a name="recipe-variable-component-dependency-name"></a>`component_dependency_name` – \(Optional\) The name of the component dependency to query\. Omit this segment to query the component that this recipe defines\. You can specify only direct dependencies\.

`kernel:rootPath`  
The AWS IoT Greengrass Core root path\.

`iot:thingName`  
This feature is available for v2\.3\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.  
The name of the core device's AWS IoT thing\.

## Recipe examples<a name="recipe-examples"></a>

You can reference the following recipe examples to help you create recipes for your components\.

AWS IoT Greengrass curates an index of Greengrass components, called the Greengrass Software Catalog\. This catalog tracks Greengrass components that are developed by the Greengrass community\. From this catalog, you can download, modify, and deploy components to create your Greengrass applications\. For more information, see [Community components](greengrass-software-catalog.md)\.

**Topics**
+ [Hello World component recipe](#recipe-example-hello-world)
+ [Python runtime component example](#recipe-example-python-runtime)
+ [Component recipe that specifies several fields](#recipe-example-all-fields)

### Hello World component recipe<a name="recipe-example-hello-world"></a>

The following recipe describes a Hello World component that runs a Python script\. This component supports all platforms and accepts a `Message` parameter that AWS IoT Greengrass passes as an argument to the Python script\. This is the recipe for the Hello World component in the [Getting started tutorial](getting-started.md)\.

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.HelloWorld",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "My first AWS IoT Greengrass component.",
  "ComponentPublisher": "Amazon",
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "Message": "world"
    }
  },
  "Manifests": [
    {
      "Platform": {
        "os": "linux"
      },
      "Lifecycle": {
        "Run": "python3 -u {artifacts:path}/hello_world.py \"{configuration:/Message}\""
      }
    },
    {
      "Platform": {
        "os": "windows"
      },
      "Lifecycle": {
        "Run": "py -3 -u {artifacts:path}/hello_world.py \"{configuration:/Message}\""
      }
    }
  ]
}
```

------
#### [ YAML ]

```
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.HelloWorld
ComponentVersion: '1.0.0'
ComponentDescription: My first AWS IoT Greengrass component.
ComponentPublisher: Amazon
ComponentConfiguration:
  DefaultConfiguration:
    Message: world
Manifests:
  - Platform:
      os: linux
    Lifecycle:
      Run: |
        python3 -u {artifacts:path}/hello_world.py "{configuration:/Message}"
  - Platform:
      os: windows
    Lifecycle:
      Run: |
        py -3 -u {artifacts:path}/hello_world.py "{configuration:/Message}"
```

------

### Python runtime component example<a name="recipe-example-python-runtime"></a>

The following recipe describes a component that installs Python\. This component supports 64\-bit Linux devices\.

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.PythonRuntime",
  "ComponentDescription": "Installs Python 3.7",
  "ComponentPublisher": "Amazon",
  "ComponentVersion": "3.7.0",
  "Manifests": [
    {
      "Platform": {
        "os": "linux",
        "architecture": "amd64"
      },
      "Lifecycle": {
        "Install": "apt-get update\napt-get install python3.7"
      }
    }
  ]
}
```

------
#### [ YAML ]

```
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.PythonRuntime
ComponentDescription: Installs Python 3.7
ComponentPublisher: Amazon
ComponentVersion: '3.7.0'
Manifests:
  - Platform:
      os: linux
      architecture: amd64
    Lifecycle:
      Install: |
        apt-get update
        apt-get install python3.7
```

------

### Component recipe that specifies several fields<a name="recipe-example-all-fields"></a>

The following component recipe uses several recipe fields\.

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.FooService",
  "ComponentDescription": "Complete recipe for AWS IoT Greengrass components",
  "ComponentPublisher": "Amazon",
  "ComponentVersion": "1.0.0",
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "TestParam": "TestValue"
    }
  },
  "ComponentDependencies": {
    "BarService": {
      "VersionRequirement": "^1.1.0",
      "DependencyType": "SOFT"
    },
    "BazService": {
      "VersionRequirement": "^2.0.0"
    }
  },
  "Manifests": [
    {
      "Platform": {
        "os": "linux",
        "architecture": "amd64"
      },
      "Lifecycle": {
        "Install": {
          "Skipif": "onpath git",
          "Script": "sudo apt-get install git"
        }
      },
      "Artifacts": [
        {
          "URI": "s3://DOC-EXAMPLE-BUCKET/hello_world.zip",
          "Unarchive": "ZIP"
        },
        {
          "URI": "s3//DOC-EXAMPLE-BUCKET/hello_world_linux.py"
        }
      ]
    },
    {
      "Lifecycle": {
        "Install": {
          "Skipif": "onpath git",
          "Script": "sudo apt-get install git",
          "RequiresPrivilege": "true"
        }
      },
      "Artifacts": [
        {
          "URI": "s3://DOC-EXAMPLE-BUCKET/hello_world.py"
        }
      ]
    }
  ]
}
```

------
#### [ YAML ]

```
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.FooService
ComponentDescription: Complete recipe for AWS IoT Greengrass components
ComponentPublisher: Amazon
ComponentVersion: '1.0.0'
ComponentConfiguration:
  DefaultConfiguration:
    TestParam: TestValue
ComponentDependencies:
  BarService:
    VersionRequirement: ^1.1.0
    DependencyType: SOFT
  BazService: VersionRequirement: ^2.0.0
Manifests:
  - Platform:
      os: linux
      architecture: amd64
    Lifecycle:
      Install:
        Skipif: onpath git
        Script: sudo apt-get install git
    Artifacts:
      - URI: s3://DOC-EXAMPLE-BUCKET/hello_world.zip
        Unarchive: ZIP
      - URI: s3//DOC-EXAMPLE-BUCKET/hello_world_linux.py
  - Lifecycle:
      Install:
        Skipif: onpath git
        Script: sudo apt-get install git
        RequiresPrivilege: true
    Artifacts:
      - URI: s3://DOC-EXAMPLE-BUCKET/hello_world.py
```

------
# Components<a name="greengrass-components"></a>

AWS IoT Greengrass components are software modules that you deploy to Greengrass core devices\. Components can represent applications, runtime installers, libraries, or any code that you would run on a device\. You can define components that depend on other components\. For example, you might define a component that installs Python, and then define that component as a dependency of your components that run Python applications\. When you deploy your components to your fleets of devices, Greengrass deploys only the software modules that your devices require\.

**Topics**
+ [AWS IoT Greengrass development tools](greengrass-development-tools.md)
+ [Develop AWS IoT Greengrass components](develop-greengrass-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
+ [AWS\-provided components](public-components.md)
+ [Community components](greengrass-software-catalog.md)
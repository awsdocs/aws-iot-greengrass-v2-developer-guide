# Install or update the AWS IoT Greengrass Development Kit Command\-Line Interface<a name="install-greengrass-development-kit-cli"></a>

The AWS IoT Greengrass Development Kit Command\-Line Interface \(GDK CLI\) is built on Python, so you can use `pip` to install it on your development computer\.

**Tip**  
You can also install the GDK CLI in a Python virtual environments such as [venv](https://docs.python.org/3/library/venv.html#module-venv)\. For more information, see [Virtual Environments and Packages](https://docs.python.org/3/tutorial/venv.html) in the *Python 3 documentation*\.

**To install or update the GDK CLI**

1. Run the following command to install the latest version of the GDK CLI from its [GitHub repository](https://github.com/aws-greengrass/aws-greengrass-gdk-cli)\.

   ```
   python3 -m pip install -U git+https://github.com/aws-greengrass/aws-greengrass-gdk-cli.git@v1.1.0
   ```
**Note**  
To install a specific version of the GDK CLI, replace *versionTag* with the version tag to install\. You can view version tags for the GDK CLI in its [GitHub repository](https://github.com/aws-greengrass/aws-greengrass-gdk-cli/tags)\.  

   ```
   python3 -m pip install -U git+https://github.com/aws-greengrass/aws-greengrass-gdk-cli.git@versionTag
   ```

1. <a name="gdk-cli-verify-installation"></a>Run the following command to verify that the GDK CLI installed successfully\.

   ```
   gdk --help
   ```

   If the `gdk` command isn't found, add its folder to PATH\.
   + On Linux devices, add `/home/MyUser/.local/bin` to PATH, and replace *MyUser* with the name of your user\.
   + On Windows devices, add `PythonPath\Scripts` to PATH, and replace *PythonPath* with the path to the Python folder on your device\.

You can now use the GDK CLI to create, build, and publish Greengrass components\. For more information about how to use the GDK CLI, see [AWS IoT Greengrass Development Kit Command\-Line Interface commands](greengrass-development-kit-cli-commands.md)\.
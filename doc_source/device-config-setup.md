# Configure your device to run IDT tests<a name="device-config-setup"></a>

To enable IDT to run tests for device qualification, you must configure your host computer to access your device, and configure user permissions on your device\.

## Install Java on the host computer<a name="install-java-for-idt"></a>

Starting with IDT v4\.2\.0, the optional qualification tests for AWS IoT Greengrass require Java to run\.

You can use Java version 8 or greater\. We recommend [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\.

## Configure your host computer to access your device under test<a name="configure-host"></a>

IDT runs on your host computer and must be able to use SSH to connect to your device\. There are two options to allow IDT to gain SSH access to your devices under test:

1. Follow the instructions here to create an SSH key pair and authorize your key to sign in to your device under test without specifying a password\.

1. Provide a user name and password for each device in the `device.json` file\. For more information, see [Configure device\.json](set-config.md#device-config)\.

You can use any SSL implementation to create an SSH key\. The following instructions show you how to use [SSH\-KEYGEN](https://www.ssh.com/ssh/keygen/) or [ PuTTYgen](https://www.ssh.com/ssh/putty/windows/puttygen) \(for Windows\)\. If you are using another SSL implementation, refer to the documentation for that implementation\.

IDT uses SSH keys to authenticate with your device under test\. 

**To create an SSH key with SSH\-KEYGEN**

1. Create an SSH key\.

   You can use the Open SSH ssh\-keygen command to create an SSH key pair\. If you already have an SSH key pair on your host computer, it is a best practice to create a SSH key pair specifically for IDT\. This way, after you have completed testing, your host computer can no longer connect to your device without entering a password\. It also allows you to restrict access to the remote device to only those who need it\.
**Note**  
Windows does not have an installed SSH client\. For information about installing an SSH client on Windows, see [Download SSH Client Software](https://www.ssh.com/ssh/#sec-Download-client-software)\.

   The ssh\-keygen command prompts you for a name and path to store the key pair\. By default, the key pair files are named `id_rsa` \(private key\) and `id_rsa.pub` \(public key\)\. On macOS and Linux, the default location of these files is `~/.ssh/`\. On Windows, the default location is `C:\Users\<user-name>\.ssh`\.

   When prompted, enter a key phrase to protect your SSH key\. For more information, see [Generate a New SSH key](https://www.ssh.com/ssh/keygen/)\.

1. Add authorized SSH keys to your device under test\.

   IDT must use your SSH private key to sign in to your device under test\. To authorize your SSH private key to sign in to your device under test, use the ssh\-copy\-id command from your host computer\. This command adds your public key into the `~/.ssh/authorized_keys` file on your device under test\. For example:

   $ ssh\-copy\-id *<remote\-ssh\-user>*@*<remote\-device\-ip>*

   Where *remote\-ssh\-user* is the user name used to sign in to your device under test and *remote\-device\-ip* is the IP address of the device under test to run tests against\. For example:

   ssh\-copy\-id pi@192\.168\.1\.5

   When prompted, enter the password for the user name you specified in the ssh\-copy\-id command\.

   ssh\-copy\-id assumes the public key is named `id_rsa.pub` and is stored the default location \(on macOS and Linux, `~/.ssh/` and on Windows, `C:\Users\<user-name>\.ssh`\)\. If you gave the public key a different name or stored it in a different location, you must specify the fully qualified path to your SSH public key using the \-i option to ssh\-copy\-id \(for example, ssh\-copy\-id \-i \~/my/path/myKey\.pub\)\. For more information about creating SSH keys and copying public keys, see [SSH\-COPY\-ID](https://www.ssh.com/ssh/copy-id)\.

**To create an SSH key using PuTTYgen \(Windows only\)**

1. Make sure you have the OpenSSH server and client installed on your device under test\. For more information, see [OpenSSH](https://www.openssh.com/)\.

1. Install [PuTTYgen](https://www.puttygen.com/) on your device under test\.

1. Open PuTTYgen\.

1. Choose **Generate** and move your mouse cursor inside the box to generate a private key\.

1. From the **Conversions** menu, choose **Export OpenSSH key**, and save the private key with a `.pem` file extension\.

1. Add the public key to the `/home/<user>/.ssh/authorized_keys` file on device under test\.

   1. Copy the public key text from the PuTTYgen window\.

   1. Use PuTTY to create a session on your device under test\.

      1. From a command prompt or Windows Powershell window, run the following command:

          C:/*<path\-to\-putty>*/putty\.exe \-ssh *<user>*@*<dut\-ip\-address>*  

      1. When prompted, enter your device's password\.

      1. Use vi or another text editor to append the public key to the `/home/<user>/.ssh/authorized_keys` file on your device under test\.

1. Update your `device.json` file with your user name, the IP address, and path to the private key file that you just saved on your host computer for each device under test\. For more information, see [Configure device\.json](set-config.md#device-config)\. Make sure you provide the full path and file name to the private key and use forward slashes \('/'\)\. For example, for the Windows path `C:\DT\privatekey.pem`, use `C:/DT/privatekey.pem` in the `device.json` file\. 

## Configure user credentials for Windows devices<a name="configure-windows-user-for-idt"></a>

To qualify a Windows\-based device, you must configure user credentials in the LocalSystem account on the device under test for the following users: 
+ The default Greengrass user \(`ggc_user`\)\.
+ The user that you use to connect to the device under test\. You configure this user in the [`device.json` file](set-config.md#device-config)\.

You must create each user in the LocalSystem account on the device under test, and then store the user name and password for the user in the Credential Manager instance for the LocalSystem account\. <a name="set-up-windows-device-environment-procedure"></a>

**To configure users on Windows devices**

1. Open the Windows Command Prompt \(`cmd.exe`\) as an administrator\.

1. Create the users in the LocalSystem account on the Windows device\. Run the following command for each user that you want to create\. For the default Greengrass user, replace *user\-name* with `ggc_user`\. Replace *password* with a secure password\.

   ```
   net user /add user-name password
   ```

1. Download and install the [PsExec utility](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) from Microsoft on the device\. 

1. Use the PsExec utility to store the user name and password for the default user in the Credential Manager instance for the LocalSystem account\. 

   Run the following command for each user that you want to configure in Credential Manager\. For the default Greengrass user, replace *user\-name* with `ggc_user`\. Replace *password* with the user's password that you set earlier\.

   ```
   psexec -s cmd /c cmdkey /generic:user-name /user:user-name /pass:password
   ```

   If the **PsExec License Agreement** opens, choose **Accept** to agree to the license and run the command\.
**Note**  
On Windows devices, the LocalSystem account runs the Greengrass nucleus, and you must use the PsExec utility to store user information in the LocalSystem account\. Using the Credential Manager application stores this information in the Windows account of the currently logged on user, instead of the LocalSystem account\.

## Configure user permissions on your device<a name="root-access"></a>

IDT performs operations on various directories and files in a device under test\. Some of these operations require elevated permissions \(using sudo\)\. To automate these operations, IDT for AWS IoT Greengrass V2 must be able to run commands with sudo without being prompted for a password\.

Follow these steps on the device under test to allow sudo access without being prompted for a password\. 

**Note**  
`username` refers to the SSH user used by IDT to access the device under test\.

**To add the user to the sudo group**

1. On the device under test, run `sudo usermod -aG sudo <username>`\.

1. Sign out and then sign back in for changes to take effect\.

1. To verify your user name was added successfully, run sudo echo test\. If you are not prompted for a password, your user is configured correctly\.

1. Open the `/etc/sudoers` file and add the following line to the end of the file:

   `<ssh-username> ALL=(ALL) NOPASSWD: ALL`

## Configure a custom token exchange role<a name="configure-custom-tes-role-for-idt"></a>

You can choose to use a custom IAM role as the token exchange role that the device under test assumes to interact with AWS resources\. For information about creating an IAM role, see [Creating IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html) in the *IAM User Guide*\.

You must meet the following requirements to allow IDT to use your custom IAM role\. We strongly recommend that you add only the minimum required policy actions to this role\.
+ The [userdata\.json](set-config.md#custom-token-exchange-role-idt) configuration file must be updated to set the `GreengrassV2TokenExchangeRole` parameter to `true`\.
+ The custom IAM role must be configured with the following minimum trust policy:

  ```
  {
     "Version":"2012-10-17",
     "Statement":[
        {
           "Effect":"Allow",
           "Principal":{
              "Service":[
                 "credentials.iot.amazonaws.com",
                 "lambda.amazonaws.com", 
                 "sagemaker.amazonaws.com" 
              ]
           },
           "Action":"sts:AssumeRole"
        }
     ]
  }
  ```
+ The custom IAM role must be configured with the following minimum permissions policy:

  ```
  {
     "Version":"2012-10-17",
     "Statement":[
        {
           "Effect":"Allow",
           "Action":[
              "iot:DescribeCertificate",
              "logs:CreateLogGroup",
              "logs:CreateLogStream",
              "logs:PutLogEvents",
              "logs:DescribeLogStreams",
              "iot:Connect",
              "iot:Publish",
              "iot:Subscribe",
              "iot:Receive",
              "iot:ListThingPrincipals", 
              "iot:GetThingShadow",
              "iot:UpdateThingShadow",
              "s3:GetBucketLocation",
              "s3:GetObject",
              "s3:PutObject",
              "s3:AbortMultipartUpload",
              "s3:ListMultipartUploadParts"
           ],
           "Resource":"*"
        }
     ]
  }
  ```
+ The name of the custom IAM role must match the IAM role resource that you specify in the IAM permissions for the test user\. By default, the [test user policy](dev-tst-prereqs.md#configure-idt-permissions) allows access to IAM roles that have the `idt-` prefix in their role names\. If your IAM role name doesn't use this prefix, add the `arn:aws:iam::*:role/custom-iam-role-name` resource to the `roleAliasResources` statement and the `passRoleForResources` statement in your test user policy, as shown in the following examples:

    
**Example `passRoleForResources` statement**  

  ```
  {
     "Sid":"passRoleForResources",
     "Effect":"Allow",
     "Action":"iam:PassRole",
     "Resource":"arn:aws:iam::*:role/custom-iam-role-name",
     "Condition":{
        "StringEquals":{
           "iam:PassedToService":[
              "iot.amazonaws.com",
              "lambda.amazonaws.com",
              "greengrass.amazonaws.com"
           ]
        }
     }
  }
  ```  
**Example `roleAliasResources` statement**  

  ```
  {
     "Sid":"roleAliasResources",
     "Effect":"Allow",
     "Action":[
        "iot:CreateRoleAlias",
        "iot:DescribeRoleAlias",
        "iot:DeleteRoleAlias",
        "iot:TagResource",
        "iam:GetRole"
     ],
     "Resource":[
        "arn:aws:iot:*:*:rolealias/idt-*",
        "arn:aws:iam::*:role/custom-iam-role-name"
     ]
  }
  ```

## Configure your device to test optional features<a name="optional-feature-config"></a>

This section describes the device requirements to run IDT tests for optional Docker and machine learning \(ML\) features\. You must make sure your device meets these requirements only if you want to test these features\. Otherwise, continue to [Configure IDT settings to run the AWS IoT Greengrass qualification suite](set-config.md)\.

**Topics**
+ [Docker qualification requirements](#idt-config-docker-components)
+ [ML qualification requirements](#idt-config-ml-components)
+ [HSM qualification requirements](#idt-config-hsm-components)

### Docker qualification requirements<a name="idt-config-docker-components"></a>

IDT for AWS IoT Greengrass V2 provides Docker qualification tests to validate that your devices can use the AWS\-provided [Docker application manager](docker-application-manager-component.md) component to download Docker container images that you can run using custom Docker container components\. For information about creating custom Docker components, see [Run a Docker container](run-docker-container.md)\.

To run Docker qualification tests, your devices under test must meet the following requirements to deploy the Docker application manager component\.
+ <a name="docker-engine-requirement"></a>[Docker Engine](https://docs.docker.com/engine/) 1\.9\.1 or later installed on the Greengrass core device\. Version 20\.10 is the latest version that is verified to work with the AWS IoT Greengrass Core software\. You must install Docker directly on the core device before you deploy components that run Docker containers\.
+ <a name="docker-daemon-requirement"></a>The Docker daemon started and running on the core device before you deploy this component\. 
+ <a name="docker-user-permissions-requirement"></a>The system user that runs a Docker container component must have root or administrator permissions, or you must configure Docker to run it as a non\-root or non\-admistrator user\.
  + On Linux devices, you can add a user to the `docker` group to call `docker` commands without `sudo`\.
  + On Windows devices, you can add a user to the `docker-users` group to call `docker` commands without adminstrator privileges\.

------
#### [ Linux or Unix ]

  To add `ggc_user`, or the non\-root user that you use to run Docker container components, to the `docker` group, run the following command\.

  ```
  sudo usermod -aG docker ggc_user
  ```

  For more information, see [Manage Docker as a non\-root user](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)\.

------
#### [ Windows Command Prompt \(CMD\) ]

  To add `ggc_user`, or the user that you use to run Docker container components, to the `docker-users` group, run the following command as an administrator\.

  ```
  net localgroup docker-users ggc_user /add
  ```

------
#### [ Windows PowerShell ]

  To add `ggc_user`, or the user that you use to run Docker container components, to the `docker-users` group, run the following command as an administrator\.

  ```
  Add-LocalGroupMember -Group docker-users -Member ggc_user
  ```

------

### ML qualification requirements<a name="idt-config-ml-components"></a>

IDT for AWS IoT Greengrass V2 provides ML qualification tests to validate that your devices can use the AWS\-provided [machine learning components](machine-learning-components.md) to perform ML inference locally using the [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) or [TensorFlow Lite](https://www.tensorflow.org/lite/guide/python) ML frameworks\. For more information about running ML inference on Greengrass devices, see [Perform machine learning inference](perform-machine-learning-inference.md)\.

To run ML qualification tests, your devices under test must meet the following requirements to deploy the machine learning components\.<a name="ml-component-requirements"></a>
+ On Greengrass core devices running Amazon Linux 2 or Ubuntu 18\.04, [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later installed on the device\.
+ On Armv7l devices, such as Raspberry Pi, dependencies for OpenCV\-Python installed on the device\. Run the following command to install the dependencies\.

  ```
  sudo apt-get install libopenjp2-7 libilmbase23 libopenexr-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0 libwebp-dev
  ```
+ Raspberry Pi devices that run Raspberry Pi OS Bullseye must meet the following requirements:
  + NumPy 1\.22\.4 or later installed on the device\. Raspberry Pi OS Bullseye includes an earlier version of NumPy, so you can run the following command to upgrade NumPy on the device\.

    ```
    pip3 install --upgrade numpy
    ```
  + The legacy camera stack enabled on the device\. Raspberry Pi OS Bullseye includes a new camera stack that is enabled by default and isn't compatible, so you must enable the legacy camera stack\.<a name="raspberry-pi-bullseye-enable-legacy-camera-stack"></a>

**To enable the legacy camera stack**

    1. Run the following command to open the Raspberry Pi configuration tool\.

       ```
       sudo raspi-config
       ```

    1. Select **Interface Options**\.

    1. Select **Legacy camera** to enable the legacy camera stack\.

    1. Reboot the Raspberry Pi\.

### HSM qualification requirements<a name="idt-config-hsm-components"></a>

AWS IoT Greengrass provides [PKCS\#11 provider component](pkcs11-provider-component.md) to integrate with the PKCS Hardware Security Module \(HSM\) on the device\. The HSM setup depends on your device and the HSM module that you have chosen\. As long as the expected HSM configuration, as documented in the [IDT configuration settings](set-config.md), is provided, IDT will have the information required to run this optional feature qualification test\.
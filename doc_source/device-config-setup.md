# Configure your device to run IDT tests<a name="device-config-setup"></a>

To enable IDT to run tests for device qualification, you must configure your host computer to access your device, and configure user permissions on your device\.

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

## Configure user permissions on your device<a name="root-access"></a>

IDT performs operations on various directories and files in a device under test\. Some of these operations require elevated permissions \(using sudo\)\. To automate these operations, IDT for AWS IoT Greengrass must be able to run commands with sudo without being prompted for a password\.

Follow these steps on the device under test to allow sudo access without being prompted for a password\. 

**Note**  
`username` refers to the SSH user used by IDT to access the device under test\.

**To add the user to the sudo group**

1. On the device under test, run `sudo usermod -aG sudo <username>`\.

1. Sign out and then sign back in for changes to take effect\.

1. To verify your user name was added successfully, run sudo echo test\. If you are not prompted for a password, your user is configured correctly\.

1. Open the `/etc/sudoers` file and add the following line to the end of the file:

   `<ssh-username> ALL=(ALL) NOPASSWD: ALL`
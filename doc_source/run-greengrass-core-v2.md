# Run the AWS IoT Greengrass Core software<a name="run-greengrass-core-v2"></a>

After you [install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md), run it to connect your device to AWS IoT Greengrass\.

When you install the AWS IoT Greengrass Core software, you can specify whether to install it as a system service with [systemd](https://en.wikipedia.org/wiki/Systemd)\. If you choose this option, the installer runs the software for you and configures it to run when your device boots\.

**Important**  <a name="windows-system-service-requirement-important-note"></a>
On Windows core devices, you must set up the AWS IoT Greengrass Core software as a system service\.

**Topics**
+ [Check if the AWS IoT Greengrass Core software runs as a system service](#check-system-service)
+ [Run the AWS IoT Greengrass Core software as a system service](#run-greengrass-core-system-service)
+ [Run the AWS IoT Greengrass Core software without a system service](#run-greengrass-core-no-system-service)

## Check if the AWS IoT Greengrass Core software runs as a system service<a name="check-system-service"></a>

When you install the AWS IoT Greengrass Core software, you can specify the `--setup-system-service true` argument to install the AWS IoT Greengrass Core software as a system service\. Linux devices require the [systemd](https://en.wikipedia.org/wiki/Systemd) init system to set up the AWS IoT Greengrass Core software as a system service\. If you use this option, the installer runs the software for you and configures it to run when your device boots\. The installer outputs the following message if it successfully installs the AWS IoT Greengrass Core software as a system service\.

```
Successfully set up Nucleus as a system service
```

If you previously installed the AWS IoT Greengrass Core software and don't have the installer output, you can check if the software installed as a system service\.

**To check if the AWS IoT Greengrass Core software is installed as a system service**
+ Run the following command to check the status of the Greengrass system service\.

------
#### [ Linux or Unix \(systemd\) ]

  ```
  sudo systemctl status greengrass.service
  ```

  The response looks similar to the following example if the AWS IoT Greengrass Core software is installed as a system service and active\.

  ```
  ● greengrass.service - Greengrass Core
     Loaded: loaded (/etc/systemd/system/greengrass.service; enabled; vendor preset: disabled)
     Active: active (running) since Thu 2021-02-11 01:33:44 UTC; 4 days ago
   Main PID: 16107 (sh)
     CGroup: /system.slice/greengrass.service
             ├─16107 /bin/sh /greengrass/v2/alts/current/distro/bin/loader
             └─16111 java -Dlog.store=FILE -Droot=/greengrass/v2 -jar /greengrass/v2/alts/current/distro/lib/Greengrass...
  ```

  If `systemctl` or `greengrass.service` isn't found, the AWS IoT Greengrass Core software isn't installed as a system service\. To run the software, see [Run the AWS IoT Greengrass Core software without a system service](#run-greengrass-core-no-system-service)\.

------
#### [ Windows Command Prompt \(CMD\) ]

  ```
  sc query greengrass
  ```

  The response looks similar to the following example if the AWS IoT Greengrass Core software is installed as a Windows service and active\.

  ```
  SERVICE_NAME: greengrass
          TYPE               : 10  WIN32_OWN_PROCESS
          STATE              : 4  RUNNING
                                  (STOPPABLE, NOT_PAUSABLE, ACCEPTS_SHUTDOWN)
          WIN32_EXIT_CODE    : 0  (0x0)
          SERVICE_EXIT_CODE  : 0  (0x0)
          CHECKPOINT         : 0x0
          WAIT_HINT          : 0x0
  ```

------
#### [ PowerShell ]

  ```
  Get-Service greengrass
  ```

  The response looks similar to the following example if the AWS IoT Greengrass Core software is installed as a Windows service and active\.

  ```
  Status   Name               DisplayName
  ------   ----               -----------
  Running  greengrass         greengrass
  ```

------

## Run the AWS IoT Greengrass Core software as a system service<a name="run-greengrass-core-system-service"></a>

If the AWS IoT Greengrass Core software is installed as a system service, you can use the system service manager to start, stop, and manage the software\. For more information, see [Configure the Greengrass nucleus as a system service](configure-greengrass-core-v2.md#configure-system-service)\.

**To run the AWS IoT Greengrass Core software**
+ Run the following command to start the AWS IoT Greengrass Core software\.

------
#### [ Linux or Unix \(systemd\) ]

  ```
  sudo systemctl start greengrass.service
  ```

------
#### [ Windows Command Prompt \(CMD\) ]

  ```
  sc start greengrass
  ```

------
#### [ PowerShell ]

  ```
  Start-Service greengrass
  ```

------

## Run the AWS IoT Greengrass Core software without a system service<a name="run-greengrass-core-no-system-service"></a>

On Linux core devices, if the AWS IoT Greengrass Core software isn't installed as a system service, you can run the software's loader script to run the software\.

**To run the AWS IoT Greengrass Core software without a system service**
+ Run the following command to start the AWS IoT Greengrass Core software\. If you run this command in a terminal, you must keep the terminal session open to keep the AWS IoT Greengrass Core software running\.
  + Replace */greengrass/v2* or *C:\\greengrass\\v2* with the Greengrass root folder that you use\.

  ```
  sudo /greengrass/v2/alts/current/distro/bin/loader
  ```

  The software prints the following message if it launches successfully\.

  ```
  Launched Nucleus successfully.
  ```
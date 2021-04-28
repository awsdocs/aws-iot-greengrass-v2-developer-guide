# Troubleshooting AWS IoT Greengrass in a Docker container<a name="docker-troubleshooting"></a>

Use the following information to help you troubleshoot issues with running AWS IoT Greengrass in a Docker container and to debug issues with AWS IoT Greengrass in the Docker container\.

**Topics**
+ [Troubleshooting issues with running the Docker container](#troubleshooting-container-errors)
+ [Debugging AWS IoT Greengrass in a Docker container](#debugging-greengrass-in-docker)

## Troubleshooting issues with running the Docker container<a name="troubleshooting-container-errors"></a>

Use the following information to help troubleshoot issues with running AWS IoT Greengrass in a Docker container\.

**Topics**
+ [Error: Cannot perform an interactive login from a non TTY device\.](#docker-troubleshootin-ecr-get-login-password)
+ [Error: Unknown options: \-no\-include\-email\.](#docker-troubleshooting-cli-version)
+ [Error: A firewall is blocking file Sharing between windows and the containers\.](#docker-troubleshooting-firewall)
+ [Error: An error occurred \(AccessDeniedException\) when calling the GetAuthorizationToken operation: User: arn:aws:iam::*account\-id*:user/<user\-name> is not authorized to perform: ecr:GetAuthorizationToken on resource: \*](#docker-troubleshooting-ecr-perms)

### Error: Cannot perform an interactive login from a non TTY device\.<a name="docker-troubleshootin-ecr-get-login-password"></a>

This error can occur when you run the `aws ecr get-login-password` command\. Make sure that you installed the latest AWS CLI version 2 or version 1\. We recommend that you use the AWS CLI version 2\. For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.

### Error: Unknown options: \-no\-include\-email\.<a name="docker-troubleshooting-cli-version"></a>

This error can occur when you run the `aws ecr get-login` command\. Make sure that you have the latest AWS CLI version installed \(for example, run: `pip install awscli --upgrade --user`\)\. For more information, see [Installing the AWS Command Line Interface on Microsoft Windows](https://docs.aws.amazon.com/cli/latest/userguide/awscli-install-windows.html) in the *AWS Command Line Interface User Guide*\.

### Error: A firewall is blocking file Sharing between windows and the containers\.<a name="docker-troubleshooting-firewall"></a>

You might receive this error or a `Firewall Detected` message when running Docker on a Windows computer\. This can also occur if you are signed in on a virtual private network \(VPN\) and your network settings are preventing the shared drive from being mounted\. In that situation, turn off VPN and re\-run the Docker container\.

### Error: An error occurred \(AccessDeniedException\) when calling the GetAuthorizationToken operation: User: arn:aws:iam::*account\-id*:user/<user\-name> is not authorized to perform: ecr:GetAuthorizationToken on resource: \*<a name="docker-troubleshooting-ecr-perms"></a>

You might receive this error when running the `aws ecr get-login-password` command if you don't have sufficient permissions to access an Amazon ECR repository\. For more information, see [Amazon ECR Repository Policy Examples](https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-policy-examples.html) and [Accessing One Amazon ECR Repository](https://docs.aws.amazon.com/AmazonECR/latest/userguide/security_iam_id-based-policy-examples.html) in the *Amazon ECR User Guide*\.

## Debugging AWS IoT Greengrass in a Docker container<a name="debugging-greengrass-in-docker"></a>

To debug issues with a Docker container, you can persist the Greengrass runtime logs or attach an interactive shell to the Docker container\.

### Persist Greengrass logs outside of the Docker container<a name="debugging-docker-persist-logs"></a>

After you stop a AWS IoT Greengrass container, you can use `docker cp container-id:/greengrass/v2/logs` to access the Greengrass logs inside the Docker container\. To persist logs even after a container exits or is removed, run the AWS IoT Greengrass Docker container after bind\-mounting the `/greengrass/v2/logs` directory\. 

To bind\-mount the `/greengrass/v2/logs` directory when you start a Greengrass Docker container, do one of the following when you run a new AWS IoT Greengrass Docker container\.
+ Include `-v /tmp/logs:/greengrass/v2/logs:ro` in your `docker run` command\.

  Modify the `volumes` block in the Compose file to include the following line before you run your `docker-compose up` command\.

  ```
  volumes:
   - /tmp/logs:/greengrass/v2/logs:ro
  ```

You can then check your logs at `/tmp/logs` on your host to see Greengrass logs while AWS IoT Greengrass is running inside the Docker container\.

For information about running Greengrass Docker containers, see [Run AWS IoT Greengrass in Docker with manual provisioning](run-greengrass-docker-manual-provisioning.md) and [Run AWS IoT Greengrass in Docker with automatic provisioning](run-greengrass-docker-automatic-provisioning.md)

### Attach an interactive shell to the Docker container<a name="debugging-docker-attach-shell"></a>

When you use `docker exec` to run commands inside the Docker container, those commands are not captured in the Docker logs\. Logging your commands in the Docker logs can help you investigate the state of the Greengrass Docker container\. Do one of the following:
+ Run the following command in a separate terminal to attach your terminal's standard input, output, and error to the running container\. This enables you to view and control the Docker container from your current terminal\.

  ```
  docker attach container-id
  ```
+ Run the following command in a separate terminal\. This enables you to run your commands in interactive mode, even if the container is not attached\.

  ```
  docker exec -it container-id sh -c "command > /proc/1/fd/1"
  ```

For general AWS IoT Greengrass troubleshooting, see [Troubleshooting](troubleshooting.md)\.
# get\-debug\-password<a name="gg-cli-get-debug-password"></a>

Use the `get-debug-password` command to print a randomly generated password for the [local debug console component](local-debug-console-component.md) \(`aws.greengrass.LocalDebugConsole`\)\. The password expires 8 hours after it is generated\.

**Synopsis**  

```
greengrass-cli get-debug-password
```

**Arguments**  
None

**Output**  
The following example shows the output produced when you run this command\.  

```
$ sudo greengrass-cli get-debug-password

Username: debug
Password: bEDp3MOHdj8ou2w5de_sCBI2XAaguy3a8XxREXAMPLE
Password expires at: 2021-04-01T17:01:43.921999931-07:00
The local debug console is configured to use TLS security. The certificate is self-signed so you will need to bypass your web browser's security warnings to open the console.
Before you bypass the security warning, verify that the certificate fingerprint matches the following fingerprints.
SHA-256: 15 0B 2C E2 54 8B 22 DE 08 46 54 8A B1 2B 25 DE FB 02 7D 01 4E 4A 56 67 96 DA A6 CC B1 D2 C4 1B
SHA-1: BC 3E 16 04 D3 80 70 DA E0 47 25 F9 90 FA D6 02 80 3E B5 C1
```
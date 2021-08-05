# get\-debug\-password<a name="gg-cli-get-debug-password"></a>

Use the `get-debug-password` command to print a randomly generated password for the [local debug console component](local-debug-console-component.md) \(`aws.greengrass.LocalDebugConsole`\)\. The password expires 8 hours after it is generated\.

**Synopsis**  

```
$ sudo greengrass-cli get-debug-password
```

**Arguments**  
None

**Output**  
The following example shows the output produced when you run this command\.  

```
$ sudo greengrass-cli get-debug-password

Username: debug
Password: KPr01f9M4uMj6Arq2mxTZJ6mU7PpiczaCeg6y6Omp5A
Password will expire at: 2020-11-17T16:46:59.772539-08:00
```
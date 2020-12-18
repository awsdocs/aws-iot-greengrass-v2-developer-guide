# get\-debug\-password<a name="gg-cli-get-debug-password"></a>

Use the `get-debug-password` command to print a randomly generated password for your HTTP debug view component \(`aws.greengrass.HttpDebugView`\)\. The password expires eight hours after it is generated\. 

**Synopsis**  

```
$ greengrass-cli get-debug-password
```

**Arguments**  
None

**Output**  
The following is an example of the output produced when you run this command\.  

```
Username: debug
Password: KPr01f9M4uMj6Arq2mxTZJ6mU7PpiczaCeg6y6Omp5A
Password will expire at: 2020-11-17T16:46:59.772539-08:00
```
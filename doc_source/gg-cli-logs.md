# logs<a name="gg-cli-logs"></a>

Use the `logs` command to analyze Greengrass logs on your core device\. 

**Subcommands**
+ [get](#logs-get)
+ [list\-keywords](#logs-list-keywords)
+ [list\-log\-files](#logs-list-log-files)

## get<a name="logs-get"></a>

Collect, filter, and visualize Greengrass log files\. This command supports only JSON\-formatted log files\. You can specify the logging format in the nucleus configuration\.

**Synopsis**  

```
$ greengrass-cli logs get
    [--log-dir path/to/a/log/folder]
    [--log-file path/to/a/log/file]
    [--follow true | false ]
    [--filter <filter> ]
    [--time-window <start-time>,<end-time> ]
    [--verbose ]
    [--no-color ]
    [--before <value> ]
    [--after <value> ]
    [--syslog ]
    [--max-long-queue-size <value> ]
```

**Arguments**  
+ `--log-dir`, `-ld`\. The path to the directory to check for log files\. Do not use with `--syslog`\. Use a separate argument for each additional directory to specify\. You must use at least one of `--log-dir` or `--log-file`\. You can also use both arguments in a single command\. 
+ `--log-file`, `-lf`\. The paths to the log directories you want to use\. Use a separate argument for each additional directory to specify\. You must use at least one of `--log-dir` or `--log-file`\. You can also use both arguments in a single command\.
+ `--follow`, `-fol`\. Show log updates as they occur\. Greengrass CLI continues to run and reads from the specified logs\. If you specify a time window, then Greengrass CLI stops monitoring logs after all of the time windows end\.
+ `--filter`, `-f`\. The keyword, regular expressions, or key\-value pair to use as a filter\. Provide this value as a string, a regular expression, or as a key\-value pair\. Use a separate argument for each additional filter to specify\. 

  When evaluated, multiple filters specified in a single argument are separated by OR operators, and filters specified in additional arguments are combined with AND operators\. For example, if your command includes `--filter "installed" --filter "name=alpha,name=beta"`, then Greengrass CLI will filter and display log messages that contain both the keyword `installed` and a `name` key that has the values `alpha` or `beta`\.
+ `--time-window`, `-t`\. The time window for which to show log information\. You can use both exact timestamps and relative offsets\. You must provide this information in the format `<begin-time>,<end-time>`\. If you do not specify either the begin time or the end time, then the value for that option defaults to the current system date and time\. Use a separate argument for each additional time window to specify\. 

  Greengrass CLI supports the following formats for timestamps:
  + `yyyy-MM-DD`, for example, `2020-06-30`\. The time defaults to 00:00:00 when you use this format\.

    `yyyyMMDD`, for example, `20200630`\. The time defaults to 00:00:00 when you use this format\.

    `HH:mm:ss`, for example, `15:30:45`\. The date defaults to the current system date when you use this format\.

    `HH:mm:ssSSS`, for example, `15:30:45`\. The date defaults the current system date when you use this format\.

    `YYYY-MM-DD'T'HH:mm:ss'Z'`, for example, `2020-06-30T15:30:45Z`\.

    `YYYY-MM-DD'T'HH:mm:ss`, for example, `2020-06-30T15:30:45`\. 

    `yyyy-MM-dd'T'HH:mm:ss.SSS`, for example, `2020-06-30T15:30:45.250`\.

  Relative offsets specify a time period offset from the current system time\. Greengrass CLI supports the following format for relative offsets: `+|-[<value>h|hr|hours][valuem|min|minutes][value]s|sec|seconds`\. 

  For example, the following argument to specify a time window between 1 hour and 2 hours 15 minutes before the current time is `--time-window -2h15min,-1hr`\.
+ `--verbose`\. Show all fields from the log messages\. Do not use with `--syslog`\.
+ `--no-color`, `-nc`\. Remove color coding\. The default color coding for log messages uses bold red text\. Supports only UNIX\-like terminals because it uses ANSI escape sequences\.
+ `--before`, `-b`\. The number of lines to show preceding a matched log entry\. Default is 0\.
+ `--after`, `-a`\. The number of lines to show following a matched log entry\. Default is 0\.
+ `--syslog`\. Process all log files using the syslog protocol defined by RFC3164\. Do not use with `--log-dir` and `--verbose`\. The syslog protocol uses the following format: `"<$Priority>$Timestamp $Host $Logger ($Class): $Message"`\. If you do not specify a log file, then Greengrass CLI reads log messages from the following locations: `/var/log/messages`, `/var/log/syslog`, or the `/var/log/system.log`\. 
+ `--max-log-queue-size`, `-m`\. The maximum number of log entries to allocate to memory\. Use this option to optimize memory usage\. Default is 100\.

**Output**  
The following example shows the output produced when you run this command\.  

```
$ greengrass-cli logs get --verbose \
    --log-file /greengrass/v2/logs/greengrass.log \
    --filter deployment,serviceName=DeploymentService \
    --filter level=INFO \
    --time-window 2020-12-08T01:11:17,2020-12-08T01:11:22

2020-12-08T01:11:17.615Z [INFO] (pool-2-thread-14) com.aws.greengrass.deployment.DeploymentService: Current deployment finished. {DeploymentId=44d89f46-1a29-4044-ad89-5151213dfcbc, serviceName=DeploymentService, currentState=RUNNING}
2020-12-08T01:11:17.675Z [INFO] (pool-2-thread-14) com.aws.greengrass.deployment.IotJobsHelper: Updating status of persisted deployment. {Status=SUCCEEDED, StatusDetails={detailed-deployment-status=SUCCESSFUL}, ThingName=MyThing, JobId=22d89f46-1a29-4044-ad89-5151213dfcbc
```

## list\-keywords<a name="logs-list-keywords"></a>

Show suggested keywords that you can use to filter log files\.

**Synopsis**  

```
$ greengrass-cli logs list-keywords [arguments]
```

**Arguments**  
None

**Output**  
The following examples show the output produced when you run this command\.  

```
$ greengrass-cli logs list-keywords

Here is a list of suggested keywords for Greengrass log:
level=$str
thread=$str
loggerName=$str
eventType=$str
serviceName=$str
error=$str
```

```
$ greengrass-cli logs list-keywords --syslog

Here is a list of suggested keywords for syslog:
priority=$int
host=$str
logger=$str
class=$str
```

## list\-log\-files<a name="logs-list-log-files"></a>

Show log files located in a specified directory\.

**Synopsis**  

```
$ greengrass-cli logs list-log-files [arguments]
```

**Arguments**  
`--log-dir`, `-ld`\. The path to the directory to check for log files\. 

**Output**  
The following examples show the output produced when you run this command\.  

```
$ greengrass-cli logs list-log-files -ld /greengrass/v2/logs/

/greengrass/v2/logs/aws.greengrass.Nucleus.log
/greengrass/v2/logs/main.log
/greengrass/v2/logs/greengrass.log
Total 3 files found.
```

```
$ greengrass-cli logs list-log-files

No log file found.
```
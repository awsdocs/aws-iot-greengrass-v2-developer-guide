# Run the AWS IoT Greengrass qualification suite<a name="run-tests"></a>

After you [set the required configuration](set-config.md), you can start the tests\. The runtime of the full test suite depends on your hardware\. For reference, it takes approximately 30 minutes to complete the full test suite on a Raspberry Pi 3B\.

Use the following `run-suite` command to run a suite of tests\.

```
devicetester_[linux | mac | win]_x86-64 run-suite  \
    --suite-id suite-id  \
    --group-id group-id  \
    --pool-id your-device-pool \
    --test-id test-id  \
    --update-idt y|n  \
    --userdata userdata.json
```

The options for the `run-suite` command are optional\. For example, you can omit `pool-id` if you have only one device pool, which is a set of identical devices, defined in your `device.json` file\. Or, you can omit `suite-id` if you want to run the latest test suite version in the `tests` folder\.

**Note**  
IDT prompts you if a newer test suite version is available online\. For more information, see [Test suite versions](idt-greengrass-qualification.md#idt-test-suite-versions)\.

## Example commands to run the qualification suite<a name="idt-run-suite-examples"></a>

The following command line examples show you how to run the qualification tests for a device pool\. For more information about `run-suite` and other IDT commands, see [IDT for AWS IoT Greengrass commands](#bk-cli)\.

Use the following command to run all test groups in a specified test suite\. The `list-suites` command lists the test suites that are in the `tests` folder\.

```
devicetester_[linux | mac | win]_x86-64 run-suite \
    --suite-id GGV2Q_1.0.0 \
    --pool-id <pool-id> \
    --userdata userdata.json
```

Use the following command to run a specific test group in a test suite\. The `list-groups` command lists the test groups in a test suite\.

```
devicetester_[linux | mac | win]_x86-64 run-suite \
    --suite-id GGV2Q_1.0.0 \
    --group-id <group-id> \
    --pool-id <pool-id> \
    --userdata userdata.json
```

Use the following command to run a specific test case in a test group\.

```
devicetester_[linux | mac | win]_x86-64 run-suite \
    --group-id <group-id> \
    --test-id <test-id> \
    --userdata userdata.json
```

Use the following command to run multiple test cases in a test group\.

```
devicetester_[linux | mac | win]_x86-64 run-suite \
    --group-id <group-id> \
    --test-id <test-id1>,<test-id2>
    --userdata userdata.json
```

Use the following command to list all of the test cases in a test group\.

```
devicetester_[linux | mac | win]_x86-64 list-test-cases --group-id <group-id>
```

We recommend that you run the dependency checker test group to make sure all Greengrass dependencies are installed before you run related test groups\. For example:
+ Run `coredependencies` before running core qualification test groups\.

## IDT for AWS IoT Greengrass commands<a name="bk-cli"></a>

The IDT commands are located in the `<device-tester-extract-location>/bin` directory\. To run a test suite, you provide the command in the following format:

`help`  <a name="idt-command-help"></a>
Lists information about the specified command\.

`list-groups`  <a name="idt-command-list-groups"></a>
Lists the groups in a given test suite\.

`list-suites`  <a name="idt-command-list-suites"></a>
Lists the available test suites\.

`list-supported-products`  
Lists the supported products, in this case AWS IoT Greengrass versions, and test suite versions for the current IDT version\.

`list-test-cases`  
Lists the test cases in a given test group\. The following option is supported:  
+ `group-id`\. The test group to search for\. This option is required and must specify a single group\.

`run-suite`  
Runs a suite of tests on a pool of devices\. The following are some supported options:  
+ `suite-id`\. The test suite version to run\. If not specified, IDT uses the latest version in the `tests` folder\.
+ `group-id`\. The test groups to run, as a comma\-separated list\. If not specified, IDT runs all test groups in the test suite\.
+ `test-id`\. The test cases to run, as a comma\-separated list\. When specified, `group-id` must specify a single group\.
+ `pool-id`\. The device pool to test\. You must specify a pool if you have multiple device pools defined in your `device.json` file\.
+ `stop-on-first-failure`\. Configures IDT to stop running on the first failure\. Use this option with `group-id` when you want to debug the specified test groups\. Do not use this option when running a full test\-suite to generate a qualification report\.
+ `update-idt`\. Sets the response for the prompt to update IDT\. The `Y` response stops the test execution if IDT detects there is a newer version\. The `N` response continues the test execution\.
+ `userdata`\. The full path to the `userdata.json` file that contains informaation about test artifact paths\. The `userdata.json` file must be located in the *devicetester\_extract\_location*/devicetester\_ggv2\_*\[win\|mac\|linux\]*/configs/ directory\.
For more information about `run-suite` options, use the `help` option:  

```
devicetester_[linux | mac | win_x86-64] run-suite -h
```
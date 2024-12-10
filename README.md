# :mega: ChatUnitest Maven Plugin

[English](./README.md) | [中文](./Readme_zh.md)

[![Maven Central](https://img.shields.io/maven-central/v/io.github.ZJU-ACES-ISE/chatunitest-maven-plugin?color=hex&style=plastic)](https://maven-badges.herokuapp.com/maven-central/io.github.ZJU-ACES-ISE/chatunitest-maven-plugin)

## Updates
💥 Added Docker images for generating tests in an isolated sandbox environment.

💥 Introduced multi-threading capabilities for faster test generation.

💥 The plugin can now export runtime and error logs.

💥 Added support for custom prompts.

💥 Optimized algorithms to reduce token usage.

💥 Expanded configuration options. Please refer to **Running Steps** for more details.

💥 Integrated multiple related works.

## Motivation
Many have tried using ChatGPT to assist with various programming tasks, achieving good results. However, directly using ChatGPT presents some challenges: First, the generated code often does not execute correctly, leading to the adage **“five minutes of coding, two hours of debugging”**; second, integrating with existing projects is cumbersome, requiring manual interaction with ChatGPT and switching between different pages. To address these issues, we propose a **“Generate-Validate-Fix”** framework and have developed a prototype system. To facilitate usage, we created plugins that can easily integrate into existing development workflows. We have completed the development of the Maven plugin, which is now published to the Maven Central Repository for your trial and feedback. Additionally, we have launched the Chatunitest plugin in the IntelliJ IDEA Plugin Marketplace. You can search for and install ChatUniTest in the marketplace or visit the plugin page [Chatunitest: IntelliJ IDEA Plugin](https://plugins.jetbrains.com/plugin/22522-chatunitest) for more information. This latest branch integrates several related works we have reproduced, allowing you to choose according to your needs.

## Running Steps

### 1. Configure `pom.xml`

Add the chatunitest-maven-plugin configuration to the `pom.xml` file in the project for which you want to generate unit tests, and add parameters as needed:
```xml
<plugin>
    <groupId>io.github.ZJU-ACES-ISE</groupId>
    <artifactId>chatunitest-maven-plugin</artifactId>
    <!-- Required: Use the latest version -->
    <version>2.0.0</version>
    <configuration>
        <!-- Required: You must specify your OpenAI API keys. -->
        <apiKeys></apiKeys>
        <model>gpt-4o-mini</model>
        <proxy>${proxy}</proxy>
    </configuration>
</plugin>
```
Generally, you only need to provide the API key. **If you encounter an APIConnectionError, you can add your proxy IP and port number in the proxy parameter.** On Windows, you can find the proxy IP and port in Settings -> Network & Internet -> Proxy:

**Here is a detailed description of each configuration option:**

- `apiKeys`: (**Required**) Your OpenAI API keys, for example: `Key1, Key2, ...`
- `model`: (**Optional**) OpenAI model, default value: `gpt-3.5-turbo`
- `url`: (**Optional**) API for calling the model, default value: `https://api.openai.com/v1/chat/completions`
- `testNumber`: (**Optional**) Number of tests generated for each method, default value: `5`
- `maxRounds`: (**Optional**) Maximum rounds for the repair process, default value: `5`
- `minErrorTokens`: (**Optional**) Minimum token count for error messages during the repair process, default value: `500`
- `temperature`: (**Optional**) OpenAI API parameter, default value: `0.5`
- `topP`: (**Optional**) OpenAI API parameter, default value: `1`
- `frequencyPenalty`: (**Optional**) OpenAI API parameter, default value: `0`
- `presencePenalty`: (**Optional**) OpenAI API parameter, default value: `0`
- `proxy`: (**Optional**) If needed, enter your hostname and port number, for example: `127.0.0.1:7078`
- `selectClass`: (**Optional**) The class to be tested; specify the full class name if there are classes with the same name in the project.
- `selectMethod`: (**Optional**) The method to be tested.
- `tmpOutput`: (**Optional**) The output path for parsing project information, default value: `/tmp/chatunitest-info`
- `testOutput`: (**Optional**) The output path for tests generated by `chatunitest`, default value: `{basedir}/chatunitest`
- `project`: (**Optional**) Target project path, default value: `{basedir}`
- `thread`: (**Optional**) Enable or disable multi-threading, default value: `true`
- `maxThread`: (**Optional**) Maximum number of threads, default value: `CPU core count * 5`
- `stopWhenSuccess`: (**Optional**) Whether to stop after generating a successful test, default value: `true`
- `noExecution`: (**Optional**) Whether to skip the execution validation step, default value: `false`
- All these parameters can also be specified using the -D option in the command line.
- `merge`: (**Optional**) Merge all tests corresponding to each class into a test suite, default value: `true`.
- `promptPath`: (**Optional**) Path for custom prompts. Refer to the default prompt directory: `src/main/resources/prompt`.
- `obfuscate`: (**Optional**) Enable obfuscation to protect sensitive code. Default value: false.
- `obfuscateGroupIds`: (**Optional**) Group IDs to be obfuscated. Default value includes only the current project's group ID. All these parameters can also be specified using the -D option in the command line.
- `phaseType`: (**Optional**) Select the reproduction scheme; if not selected, the default chatunitest process will execute. All these parameters can also be specified using the -D option in the command line.
    - COVERUP
    - HITS
    - TELPA
    - SYMPROMPT
    - CHATTESTER
    - TESTSPARK
    - TESTPILOT
    - MUTAP

If using a local large model (e.g., code-llama), simply modify the model name and request URL as follows:
```xml
<plugin>
    <groupId>io.github.ZJU-ACES-ISE</groupId>
    <artifactId>chatunitest-maven-plugin</artifactId>
    <version>2.0.0</version>
    <configuration>
        <!-- Required: Use any string to replace your API keys -->
        <apiKeys>xxx</apiKeys>
        <model>code-llama</model>
        <url>http://0.0.0.0:8000/v1/chat/completions</url>
    </configuration>
</plugin>
```

### 1. Add the following dependency to your `pom.xml`
Similarly, add the dependency in the `pom.xml` of the project for which you want to generate unit tests:
```xml
<dependency>
    <groupId>io.github.ZJU-ACES-ISE</groupId>
    <artifactId>chatunitest-starter</artifactId>
    <version>1.4.0</version>
    <type>pom</type>
</dependency>
```

### 2. Run

**First, you need to install the project and download the required dependencies by running the `mvn install` command.**

**You can run the plugin using the following commands:**

**To generate unit tests for a target method:**

```shell
mvn chatunitest:method -DselectMethod=className#methodName
```

**To generate unit tests for a target class:**

```shell
mvn chatunitest:class -DselectClass=className
```

When executing `mvn chatunitest:method` or `mvn chatunitest:class`, you must specify `selectMethod` and `selectClass`, which can be achieved using the -D option.

Example:

```
public class Example {
    public void method1(Type1 p1, ...) {...}
    public void method2() {...}
    ...
}
```

To test the Example class and all its methods:

```shell
mvn chatunitest:class -DselectClass=Example
```

To test the method1 in the Example class (currently, ChatUnitest will generate tests for all methods named method1 in the class):

```shell
mvn chatunitest:method -DselectMethod=Example#method1
```

**To generate unit tests for the entire project:**

:warning: :warning: :warning: For large projects, this may consume a significant number of tokens, leading to considerable costs.

```shell
mvn chatunitest:project
```

**To use a target scheme for generating unit tests:**

```shell
mvn chatunitest:method -DselectMethod=className#methodName -DselectMethod=className#methodName -DphaseType=CHATTESTER
```

**To clean up generated test code:**

```shell
mvn chatunitest:clean
```

Running this command will delete all generated test code and restore your test directory.

**To manually run the generated tests:**

```shell
mvn chatunitest:copy
```

Running this command will copy all generated test code to your test folder while backing up your test directory.

If the `merge` configuration is enabled, you can run the test suite for each class.

```shell
mvn chatunitest:restore
```

Running this command will restore your test directory.

## Custom Content
If you want to customize content, such as extending FTL or using a custom testing generation scheme, you can [refer here](https://github.com/ZJU-ACES-ISE/chatunitest-core/blob/corporation/README.md#custom-content)

## Precautions
### 1.COVERUP
Initial use may result in an error. Please extract the Jacoco-integration.zip file from the resources directory to the specified directory (io \ Github \ ZJU-ACES-ISE)

### 2.  HITS
① Slices are stored in tmp \ chatunitest info \ {project name} \ methodSlice

② Weak model capability may result in the inability to generate slices

## :email: Contact Us

If you have any questions or want to learn about our experimental results, please feel free to contact us via email:

1. Corresponding author: `zjuzhichen AT zju.edu.cn`
2. Author: `yh_ch AT zju.edu.cn`

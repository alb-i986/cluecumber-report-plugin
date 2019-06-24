![cluecumber logo](documentation/img/cluecumber.png)

### Clear and Concise Cucumber Reporting

[![Apache V2 License](http://img.shields.io/badge/license-Apache%20V2-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Maven Central](https://img.shields.io/maven-central/v/com.trivago.rta/cluecumber-report-plugin.svg)](http://repo1.maven.org/maven2/com/trivago/rta/cluecumber-report-plugin/)
[![Build Status](https://travis-ci.org/trivago/cluecumber-report-plugin.svg?branch=master)](https://travis-ci.org/trivago/cluecumber-report-plugin)
[![codecov](https://codecov.io/gh/trivago/cluecumber-report-plugin/branch/master/graph/badge.svg)](https://codecov.io/gh/trivago/cluecumber-report-plugin)
[![Twitter URL](https://img.shields.io/twitter/url/http/shields.io.svg?style=social)](https://twitter.com/bischoffdev)

![Cucumber animation](documentation/img/cluecumber.gif)

![Cucumber compatible](documentation/img/cucumber-compatible-black-64.png)

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Cluecumber Report Maven Plugin](#cluecumber-report-maven-plugin)
  - [Example report](#example-report)
  - [Changelog](#changelog)
- [Prerequisites](#prerequisites)
- [Maven POM settings](#maven-pom-settings)
  - [Mandatory Configuration Parameters](#mandatory-configuration-parameters)
    - [sourceJsonReportDirectory](#sourcejsonreportdirectory)
    - [generatedHtmlReportDirectory](#generatedhtmlreportdirectory)
  - [Optional Configuration Parameters](#optional-configuration-parameters)
    - [Add Custom Information to the Report](#add-custom-information-to-the-report)
      - [Add Custom Information Using a Property](#add-custom-information-using-a-property)
      - [Add Custom Information Using a File](#add-custom-information-using-a-file)
    - [Skip Report Generation](#skip-report-generation)
    - [Fail Scenarios on Pending or Undefined Steps](#fail-scenarios-on-pending-or-undefined-steps)
    - [Auto-expand Certain Report Sections](#auto-expand-certain-report-sections)
  - [Optional Configuration Parameters for Changing the Report Design](#optional-configuration-parameters-for-changing-the-report-design)
    - [Defining a custom CSS file](#defining-a-custom-css-file)
    - [Defining custom passed, skipped and failed colors](#defining-custom-passed-skipped-and-failed-colors)
- [Running the reporting goal directly via command line](#running-the-reporting-goal-directly-via-command-line)
  - [Passing properties via command line](#passing-properties-via-command-line)
  - [Passing custom parameters via command line](#passing-custom-parameters-via-command-line)
- [Example project](#example-project)
- [Appendix](#appendix)
  - [Building](#building)
  - [Generated pages](#generated-pages)
    - [All Scenarios](#all-scenarios)
    - [Scenario Sequence](#scenario-sequence)
    - [Scenario Details](#scenario-details)
    - [All Features](#all-features)
    - [All Tags](#all-tags)
    - [All Steps](#all-steps)
    - [Scenario by Tag](#scenario-by-tag)
    - [Scenario by Feature](#scenario-by-feature)
    - [Scenario by Step](#scenario-by-step)
  - [License](#license)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Cluecumber Report Maven Plugin

This plugin creates aggregated test reports from Cucumber compatible JSON files that are generated by [Cucumber BDD](https://github.com/cucumber), [Karate](https://github.com/intuit/karate) and other frameworks.

This project was created because
- other projects were using legacy technology or were not maintained
- other projects generate HTML code in Java making it hard to adapt
- the reporting generated from other plugins showed too much unneeded or even wrong information
- other projects' memory consumption when generating reports from large JSON files was too high
- other projects did not support new Cucumber features 

**Note:** From Cluecumber 1.10.2 on, Cluecumber fully supports Cucumber's new scenario start timestamps!

If you need to run Cucumber tests in parallel, please check out our [Cucable](https://github.com/trivago/cucable-plugin) project!

## Example report

A fully generated example report can also be [viewed online](http://tech.trivago.com/cluecumber-report-plugin/)!

## Changelog

All changes are documented in the [full changelog](CHANGELOG.md).

# Prerequisites

In order to have the JSON files as a source for the Cluecumber Report generation, you need to specify this option in your Cucumber runner configuration:

*Cucumber 1.x:*
```
@CucumberOptions(
    format = {"json:target/cucumber-report/cucumber.json"}
)
```

*Cucumber >= 2.x:*
```
@CucumberOptions(
    plugin = {"json:target/cucumber-report/cucumber.json"}
)
```

This will generate JSON results for all Cucumber tests.

# Maven POM settings

```xml
<plugin>
    <groupId>com.trivago.rta</groupId>
    <artifactId>cluecumber-report-plugin</artifactId>
    <version>Check the version number above</version>
    <executions>
        <execution>
            <id>report</id>
            <phase>post-integration-test</phase>
            <goals>
                <goal>reporting</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <sourceJsonReportDirectory>${project.build.directory}/cucumber-report</sourceJsonReportDirectory>
        <generatedHtmlReportDirectory>${project.build.directory}/generated-report</generatedHtmlReportDirectory>
    </configuration>    
</plugin>
```

## Mandatory Configuration Parameters

There are two mandatory parameters that have to be specified within the Maven POM ```configuration``` section or system properties:

__Note:__ Typically, both properties point to directories inside the Maven ```target``` directory.

### sourceJsonReportDirectory

This specifies the source folder of the Cucumber JSON result files.

```xml
<configuration>
    <sourceJsonReportDirectory>c:/example/json-files</sourceJsonReportDirectory>
    ...
</configuration>
```

### generatedHtmlReportDirectory

This points to the root directory of the generated Cluecumber HTML report.

```xml
<configuration>
    <generatedHtmlReportDirectory>c:/example/my-report</generatedHtmlReportDirectory>
    ...
</configuration>
```

## Optional Configuration Parameters

### Add Custom Information to the Report

#### Add Custom Information Using a Property

The `customParameters` block can be used to define custom information that should be displayed on the report start page.

__Note:__ Underscores in the parameter names are automatically turned into spaces in the report.
Valid URLs that start with a protocol (http, https, ftp) are automatically recognized and turned into clickable links.

```xml
<configuration>
    <customParameters>
        <Custom_Parameter>This is a test</Custom_Parameter>
        <Custom_URL>http://www.google.com</Custom_URL>
    </customParameters>
    ...
</configuration>
```

The property definitions above are shown in the report like this:

![custom parameters](documentation/img/custom_params.png)

#### Add Custom Information Using a File

You can also set custom parameters by specifying the path to a `.properties` file in the `customParametersFile` property like this:

```xml
<configuration>
    <customParametersFile>path/to/your/customParameters.properties</customParametersFile>
    ...
</configuration>
```

This file needs to have a format like this:

```properties
Custom_Parameter=This is a test
Custom_URL=http://www.google.com
```

__Note:__ These custom parameters behave exactly like the ones defined by the `customParameters` property and will be added on top of already defined properties. 
If a property has the same name as an existing one, its value will be overwritten!

The property definitions above are shown in the report like this:

![custom parameters](documentation/img/custom_params.png)

### Skip Report Generation

The `skip` property is used to skip the report generation. The default value is `false`

```xml
<configuration>
    <skip>true</skip>
    ...
</configuration>
```

### Fail Scenarios on Pending or Undefined Steps

The optional `failScenariosOnPendingOrUndefinedSteps` property can be set to `true` if you scenarios should be marked as `failed` when they contain `pending` or `skipped` steps.
The default setting is `false`, meaning that those scenarios will be marked as `skipped`.

```xml
<configuration>
    <failScenariosOnPendingOrUndefinedSteps>true</failScenariosOnPendingOrUndefinedSteps>
    ...
</configuration>
```

### Auto-expand Certain Report Sections

The `expandBeforeAfterHooks`, `expandStepHooks` and `expandDocStrings` options can be set to `true` to expand or collapse before/after hooks, step hooks or docstrings respectively on scenario detail pages.

If they are not set, they default to false. This means that the report user has to use the buttons on a scenario detail page to expand those sections on demand.

```xml
<configuration>
    <expandBeforeAfterHooks>true|false</expandBeforeAfterHooks>
    <expandStepHooks>true|false</expandStepHooks>
    <expandDocStrings>true|false</expandDocStrings>
    ...
</configuration>
```

## Optional Configuration Parameters for Changing the Report Appearance

### Defining a custom report title

By default, the page html title of the report pages is `Cluecumber Report` plus the current page name, e.g. `Cluecumber Report - All Tags`.

By setting the property `customPageTitle`, this can be changed:

```xml
<configuration>
    <customPageTitle>My Report</customPageTitle>
    ...
</configuration>
```

This would lead to a report title like this:

![Custom Title](documentation/img/custom_title.png)

### Defining a custom CSS file

The `customCSS` property can be used to define a custom CSS file that will be automatically loaded on top of Cluecumber's default styles.

If you have a custom CSS file called `custom/custom.css` in your project, you could use it to change the report's background and header colors:
```css
body {
    background-color: dimgray;
}

h3, h4 {
    color: orange;
}
```

To use this files, specify it like so in your pom file or as a system property:

```xml
<configuration>
    <customCss>custom/custom.css</customCss>
    ...
</configuration>
```

When generating the report, this file is automatically included as ```cluecumber_custom.css``` and applied on top of all other styles:

![Custom CSS](documentation/img/custom_css.png)

Likewise, if you want to hide elements from the report, e.g. the total time on the start page, you can also add this to the custom css like so:

```css
[data-cluecumber-item="total-scenario-duration"] {
    display: none;
}
```

### Defining custom passed, skipped and failed colors

It is possible to set these properties to change the color scheme for passed, failed and skipped steps and scenarios
including the displayed diagrams. The values have to be valid hex colors:

```xml
<configuration>
    <customStatusColorPassed>#00ff00</customStatusColorPassed>
    <customStatusColorFailed>#ff0000</customStatusColorFailed>
    <customStatusColorSkipped>#00ffff</customStatusColorSkipped>
    ...
</configuration>    
```


# Running the reporting goal directly via command line

In some cases it may be desirable to run the reporting as a completely separate step, e.g. in CI pipelines.
This can be done by running

`mvn cluecumber-report:reporting`

directly from the command line.

__Note:__ If you want this invocation to consider the configuration that is included in your POM file,
the `configuration` block must be outside of your `executions` block. Otherwise, it only applies to the
specified execution and is ignored when you run `mvn cluecumber-report:reporting` from the command line:

```
<executions>
    <execution>
        <id>report</id>
        <phase>post-integration-test</phase>
        <goals>
            <goal>reporting</goal>
        </goals>
        <configuration>
            <!-- This configuration block applies ONLY to this execution -->
        </configuration>
    </execution>
</executions>
<configuration>
    <!-- This configuration block applies to all executions including command line invocation -->
</configuration>
```

## Passing properties via command line

You can also pass properties directly on the command line, e.g.

`mvn cluecumber-report:reporting -DsourceJsonReportDirectory=path_to_json_files -D...`

## Passing custom parameters via command line

If you want to set a [custom parameter](#customparameters), you can do it like this:

Set an empty property in your pom file's properties block:
```xml
<properties>
    <someProperty/>
</properties>
```

Also define it in the Cluecumber section in your POM:

```xml
<customParameters>
    <My_Parameter_Name>${someProperty}</Base_Url>
</customParameters>
```

When invoking the reporting, you can now pass this property via the `-D` option:
```
mvn cluecumber-report:reporting -DsomeProperty="this is cool" -D...
```

__Note:__ If you don't pass this property, Cluecumber will ignore it and not show it in the report.

# Example project

You can test the complete flow and POM configuration by checking out the [Cluecumber example project](example-project).

# Appendix

## Building

Cluecumber requires Java >= 8 and Maven >= 3.3.9.
It is available in [Maven central](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.trivago.rta%22%20AND%20a%3A%22cluecumber-report-plugin%22).

## Generated pages

### All Scenarios

This page shows an overview of all scenarios grouped by their status `passed`, `failed` or `skipped`:

![All Scenarios](documentation/img/AllScenarios.png)

### Scenario Sequence

This page shows an overview of all scenarios in running order including their individual status information:

![Scenario Sequence](documentation/img/ScenarioSequence.png)

### Scenario Details

This page shows all steps, hooks, stack traces and attachments of a single scenario:

![Scenario Details](documentation/img/ScenarioDetails.png)

### All Features

This page shows an overview of all features:

![All Features](documentation/img/AllFeatures.png)

### All Tags

This page shows an overview of all used tags in scenarios, features and example tables including their individual status information:

![All Tags](documentation/img/AllTags.png)

### All Steps

This page shows an overview of all steps in use including their individual status information:

![All Steps](documentation/img/AllSteps.png)

### Scenario by Tag

This page shows all scenarios including a specific tag:

![Scenarios by Tag](documentation/img/TagDetails.png)

### Scenario by Feature

This page shows all scenarios belonging to a specific feature:

![Scenarios by Feature](documentation/img/FeatureDetails.png)

### Scenario by Step

This page shows all scenarios that include a specific step:

![Scenarios by Step](documentation/img/StepDetails.png)

## License

Copyright 2018 trivago NV

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

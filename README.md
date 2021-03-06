# Gradle Test Logger Plugin
[![CircleCI branch](https://img.shields.io/circleci/project/github/radarsh/gradle-test-logger-plugin/develop.svg?label=circleci+build)](https://circleci.com/gh/radarsh/gradle-test-logger-plugin/tree/develop)
[![AppVeyor branch](https://img.shields.io/appveyor/ci/radarsh/gradle-test-logger-plugin/develop.svg?label=appveyor+build)](https://ci.appveyor.com/project/radarsh/gradle-test-logger-plugin/branch/develop)
[![Coveralls branch](https://img.shields.io/coveralls/radarsh/gradle-test-logger-plugin/develop.svg)](https://coveralls.io/github/radarsh/gradle-test-logger-plugin?branch=develop)
[![Gradle plugin portal](https://img.shields.io/github/tag/radarsh/gradle-test-logger-plugin.svg?label=gradle+plugin+portal)](https://plugins.gradle.org/plugin/com.adarshr.test-logger)
[![License](https://img.shields.io/github/license/radarsh/gradle-test-logger-plugin.svg)](https://github.com/radarsh/gradle-test-logger-plugin/blob/develop/LICENSE)

A Gradle plugin for printing beautiful logs on the console while running tests.

## Screenshots

### Standard theme

![Standard theme](docs/images/standard.gif)

### Mocha theme

![Mocha theme](docs/images/mocha.gif)

Scroll down for more themes and customisation options or visit the [screenshots page](docs/SCREENSHOTS.md) for more demos.

## Usage

### Modern Gradle

```groovy
plugins {
    id 'com.adarshr.test-logger' version '1.5.0'
}
```

### Gradle < 2.1

```groovy
buildscript {
    repositories {
        maven {
            url 'https://plugins.gradle.org/m2/'
        }
    }
    dependencies {
        classpath 'com.adarshr:gradle-test-logger-plugin:1.5.0'
    }
}

apply plugin: 'com.adarshr.test-logger'
```

## Configuration

All the below configuration settings can either be specified in `build.gradle` file or be set at runtime using system
properties or both. For instance, we could have `theme` set to `mocha` in `build.gradle` file but it can be overridden
to be `standard` at runtime by using `-Dtestlogger.theme=standard` on the command line. Since they're system properties
we have a number of ways of specifying them including `JAVA_OPTS` and `gradle.properties`.

The convention used for determining the name of the system property is `testlogger.<configuration setting>`.

- [Switch themes](#switch-themes)
- [Hide exceptions](#hide-exceptions)
- [Define slow threshold](#define-slow-threshold)
- [Hide summary](#hide-summary)
- [Show standard streams](#show-standard-streams)
- [Filter standard streams](#filter-standard-streams)
- [Filter test results](#filter-test-results)

### Switch themes

```groovy
testlogger {
    theme 'mocha'
}
```

The following themes are currently supported:

1. `plain` - displays no colours or Unicode symbols
2. `standard` - displays colours but no Unicode symbols
3. `mocha` - similar to what [Mocha's](https://github.com/mochajs/mocha) [spec reporter](https://mochajs.org/#spec)
prints, with colours and Unicode symbols
4. `plain-parallel` - similar to the `plain` theme but supports parallel test execution
5. `standard-parallel` - similar to the `standard` theme but supports parallel test execution
6. `mocha-parallel` - similar to the `mocha` theme but supports parallel test execution

### Hide exceptions

By default, the `showExceptions` flag is turned on. This shows why the tests failed including the location of the
failure. Of course, you can switch off this slightly more verbose logging by setting `showExceptions` to `false`.

```groovy
testlogger {
    showExceptions false
}
```

### Define slow threshold

Tests that are too slow will have their duration logged. However, "slow" is a relative terminology varying widely
depending on the type of tests being executed, environment, kind of project and various other factors. Therefore you
can define what you consider as slow to suit your needs.

```groovy
testlogger {
    slowThreshold 5000
}
```

The default value of `slowThreshold` is `2` seconds. So all tests that take longer than a second to run will have their
actual execution time logged.

If you want to turn off the logging of time taken completely, simply set the threshold to a very large value.

Please note that in themes that support colours, the duration is displayed using a warning style if it is greater than
half the slow threshold. For instance, if `slowThreshold` is 5 seconds any tests that take longer than 2.5 seconds to
run would have their durations logged using a warning style and those that take longer than 5 seconds to run using an
error style.

### Hide summary

By default, a useful summary containing a breakdown of passing, failing and skipped tests along with the total time
taken to execute all the tests is shown. Of course, you can disable this if you prefer a more succinct output.

```groovy
testlogger {
    showSummary false
}
```

### Show standard streams

The display of standard output and error streams alongside the test logs can be controlled using the below configuration.

```groovy
testlogger {
    showStandardStreams true
}
```

### Filter standard streams

If the display standard output and error streams is enabled, it can often produce too much output to overwhelm anyone.
Fortunately, we can filter this output based on the type of the test result.

```groovy
testlogger {
    showStandardStreams true
    showPassedStandardStreams false
    showSkippedStandardStreams false
    showFailedStandardStreams true
}
```

All the three filter flags are enabled by default. In other words, the standard stream output is not filtered if
`showStandardStreams` is enabled but none of the filter flags are configured.

If `showStandardStreams` is set to `false`, the filter flags don't have any effect.

### Filter test results

Sometimes it is useful to hide test results of a certain type. For instance, if an application has hundreds of tests, the
sheer volume of the output produced by passing tests could be enough to bury any valuable test failures. Similarly there
might be a need to hide skipped tests or in rare instances even the failed ones.

We can perform test result filtering by using the below settings.

```groovy
testlogger {
    showPassed false
    showSkipped false
    showFailed true
}
```

By default all the above three flags are turned on. If you have chosen to display standard streams by setting
`showStandardStreams` flag to `true`, any output produced by filtered out tests will not be displayed.

## FAQ

### Does it work on Windows?

Mostly. The `standard` and `plain` themes work out of the box but you might have to make a few modifications to your
system settings to see Unicode symbols when using the `mocha` theme.

1. Set or update `JAVA_OPTS` with the system property `-Dfile.encoding=UTF-8`
2. Change the terminal code page to 65001 by executing `chcp 65001`

### How to disable colours and Unicode symbols at runtime such as on Jenkins consoles?

You can switch off ANSI control characters and Unicode symbols by adding `--console=plain` to your Gradle command line.

### Does it support parallel test execution?

Yes. You will need to switch to a suitable parallel theme though. This can be one of `plain-parallel`, `standard-parallel` or
`mocha-parallel`. The parallel themes are specially designed to work with a setting of
[`maxParallelForks`](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.testing.Test.html#org.gradle.api.tasks.testing.Test:maxParallelForks)
greater than 1. They achieve this by sacrificing the ability to group tests and thus some readability is lost.

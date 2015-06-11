page_title: clojure | Documentation | Shippable
page_description: How to configure shippable.yml file for clojure
page_keywords: yml,clojure,lein,lein2

# Clojure

***Clojure Page***

This section helps you to create a shippable.yml file for your clojure project:

- Set the appropriate language and the version number. Clojure minions use `lein` by default to set the runtime platform:

        # language setting
        language: clojure
        # lein tag
        lein: lein2

- Use **install** key to install the required dependencies for your project:

        install: lein protobuf install

## Test scripts

Use **script** key in shippable.yml file to specify what command to run tests with. The default command to run leiningen test suite is **lein test**:

        script: lein test

## Build Examples

This sample will help you get started with Shippable. The testing
framework used here is [Clojure.test](http://richhickey.github.io/clojure/clojure.test-api.html).

[Clojure Sample](https://github.com/shippableSamples/sample_clojure)

[Clojure Sample with
MongoDB](https://github.com/shippableSamples/sample_clojure_mongodb)

[Clojure Sample with
PostgreSQL](https://github.com/shippableSamples/sample_clojure_postgres)

Copy the test and code coverage output into the special folders
Shippable/testresults and Shippable/codecoverage to get the reports
parsed. The test report must be in the JUnit XML format and code
coverage report in the Cobertura XML format.

We need the yml file to analyze your project details. So add the
shippable.yml file to the root of your repo by specifying:

**language :** Clojure is used in this sample project

**lein :** lein2 is the version of Leinigen used in this sample project.

Here is the complete yml file for sample_clojure:

```yaml
# Language setting
language: clojure

lein:
  - lein2

# Create directories for test and coverage reports
before_script:
  - mkdir -p shippable/testresults
  - mkdir -p shippable/codecoverage

# Running the test with Leiningen
script:
  - cd sample
  - lein test
```

Enable the repo sample_clojure and run it using an Ubuntu minion. Once
the build finishes execution, you can check for the console output, test
and codecoverage results on the respective build's page.


page_title: java
page_description: How to configure shippable.yml file for java
page_keywords: yml,java,maven,oraclejdk6,openjdk6,oraclejdk7,openjdk7

# Java

This section helps you to create a shippable.yml file for your Java
project:

- Set the appropriate language and the jdk. You can test against Openjdk6, Openjdk7, Oraclejdk7 and Oraclejdk8 for a single push by adding more entries. Java minions use `jdk` by default to set the runtime platform:

         # language setting
         language: java
         # jdk tag
         jdk:
           - openjdk7
           - oraclejdk7
           - openjdk6
           - oraclejdk8

## Testing using Java build tool

### Maven

- If your repository root has pom.xml file, then our Java builder will use Maven 3 to build it. By default it will run the test using **mvn test**.
- Java builder will execute the below line to install project dependencies with Maven before it starts running tests.

         mvn install -DskipTests=true

### Gradle

- If your repository root has "build.gradle", then our Java builder will use gradle to build it. By default it will use **gradle check** to run the test.
- Java builder will execute the below line to install the project dependencies with gradle.

         gradle assemble

### Ant

- If your repository root does not have gradle or maven files, then our Java builder will use Ant to build it. By default it will use **Ant test** to run the test suite.
- Since there is no standard way to install Ant dependencies, you will need to specify the **install:** key to install your project dependencies with Ant.

         language: java
         install: ant deps

Save the test output in shippable/testresults and the codecoverage output in shippable/codecoverage folder to get the reports parsed. If the test and codecoverage output is not saved as specified, you will not find the reports in our CI.

## Build Examples

The goal of this code samples is to show you how to set up and build
your repo on shippable. These projects use [cobertura](http://cobertura.github.io/cobertura/) and [Junit](http://junit.org/).

[Java Sample](https://github.com/shippableSamples/sample_java)

[Java Sample with multi-module Maven
build](https://github.com/shippableSamples/sample-java-maven-reactor)

[Java Sample with
MySQL](https://github.com/shippableSamples/sample_java_mysql)

[Java Sample with
Postgres](https://github.com/shippableSamples/sample_java_postgres)

[Java Sample with
MongoDB](https://github.com/shippableSamples/sample_java_mongo)

Keep the test and code coverage output in the special folders
Shippable/testresults and Shippable/codecoverage to get parsed reports.
Test results must be in Junit format.

We need the yml file to analyze the project details. So add the
shippable.yml file to the root of your repo by specifying:

**language :** Specify the language used to create the project. Java is
used in this sample project.

**jdk :** Specify the jdk against which your build needs to run. The
sample uses oraclejdk7, openjdk6 and openjdk7.

**script :** Specify the command to run the test and code coverage using
the script key. Specify the path of the output files
(shippable/testresults and shippable/codecoverage) in the
"build.properties" file for this project.

**notification alerts:** Email notifications are added to get alerts
about the build status.

This is the complete yml file for sample_java project:

```yaml
language: java

jdk:
   - openjdk7
   - oraclejdk7
       - openjdk6
       - oraclejdk8

    after_success:
       - mvn clean cobertura:cobertura
       - mvn test

    notifications:
      email:
          recipients:
         - exampleone@org.com
         - exampletwo@org.com
```

Create a project by enabling the repo sample_java and run it using an
Ubuntu minion. Once the build finishes execution, you can check for the
console output, test and codecoverage results on the respective build's
page.

## Multi-module Maven builds

When using multi-module (Reactor) builds, please remember to output all
the coverage and tests reports to the (top-level) repository directory.
This can be tricky, as the Cobertura plugin resolves output directory
differently from Surefire plugin. The most straightforward way of
dealing with this issue is to define plugin configuration in the
top-level module, using `shippable/codecoverage` path for Cobertura
plugin and `../shippable/testresults` for Surefire plugin:

```xml
<plugin>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>cobertura-maven-plugin</artifactId>
  <version>2.6</version>
  <configuration>
    <format>xml</format>
    <maxmem>256m</maxmem>
    <aggregate>true</aggregate>
    <outputDirectory>shippable/codecoverage</outputDirectory>
  </configuration>
</plugin>
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-surefire-plugin</artifactId>
  <version>2.17</version>
  <configuration>
    <redirectTestOutputToFile>true</redirectTestOutputToFile>
    <reportsDirectory>../shippable/testresults</reportsDirectory>
  </configuration>
  <dependencies>
    <dependency>
      <groupId>org.apache.maven.surefire</groupId>
      <artifactId>surefire-junit4</artifactId>
      <version>2.7.2</version>
    </dependency>
  </dependencies>
</plugin>
```

See [the following sample](https://github.com/shippableSamples/sample-java-maven-reactor)
for details.

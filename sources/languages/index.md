page_title: Languages
page_description: supported languages on Shippable
page_keywords: languages, questions, documentation, shippable, ruby, node, java, .net, python, scala

# Language Specific Help

We support the following languages:

-  [clojure](clojure.md)
-  [go](go.md)
-  [java](java.md)
-  [nodejs](nodejs.md)
-  [python](python.md)
-  [ruby](ruby.md)
-  [scala](scala.md)
-  [php](php.md)

This list keeps growing and if you need something that you dont see
here, drop us a line at support @ shippable.com

## Clojure

This section helps you to create a shippable.yml file for your clojure
project:

- Set the appropriate language and the version number. Clojure minions use `lein` by default to set the runtime platform:

        # language setting
        language: clojure
        # lein tag
        lein: lein2

- Use **install** key to install the required dependencies for your project:

        install: lein protobuf install

- **Test scripts**: Use **script** key in shippable.yml file to specify what command to run tests with. The default command to run leiningen test suite is **lein test**:

        script: lein test

### Build Examples

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

## Go

This section helps you to create a shippable.yml file for your GO
project:

- Set the appropriate language and the version number. You can test against multiple versions for a single push by adding more entries. GO minions use `go` by default to set the runtime platform:

         # language setting
         language: go
         # version numbers
         go:
           - 1.1
           - 1.2
           - 1.3
           - release
           - tip

- Install dependencies for your project using the **install** key:

         install:
           - go get github.com/onsi/gomega
           - go get github.com/onsi/ginkgo

- **Test scripts** : Use the **script** key in the shippable.yml file to specify what command to run tests with:

         # command to run tests
         script: go test -v ./...

### Build Examples

This sample will help you get started with Shippable. The testing
framework used here is [Ginkgo](http://onsi.github.io/ginkgo/).

[Go Sample](https://github.com/shippableSamples/sample_go)

[Go Sample with MongoDB](https://github.com/shippableSamples/sample_go_mongo)

[Go Sample with PostgreSQL](https://github.com/shippableSamples/sample_go_postgres)

Copy the test and code coverage output into the special folders
Shippable/testresults and Shippable/codecoverage to get the reports
parsed. The test report must be in JUnit format.

We need the yml file to analyze your project details. So add the
shippable.yml file to the root of your repo by specifying:

**language :** Go is used in this sample project

**version number :** 1.2 is the version used in this sample project.

Here is the complete yml file for sample_go

```yaml
language: go

go:
  - 1.2

install:
  - go get code.google.com/p/go.tools/cmd/cover

# Make folders for the reports
before_script:
  - mkdir -p shippable/testresults
  - mkdir -p shippable/codecoverage

script:
  - export GOPATH=$PWD
  - export PATH=$PATH:$GOPATH/bin
  - go get github.com/t-yuki/gocover-cobertura
  - go get github.com/onsi/gomega
  - go get github.com/onsi/ginkgo
  - go install github.com/OneRedOak/sample
  - go test -coverprofile=coverage.txt -covermode count github.com/OneRedOak/sample
  - gocover-cobertura < coverage.txt > coverage.xml

after_script:
  - mv $PWD/src/github.com/OneRedOak/sample/junit.xml $PWD/shippable/testresults/
  - mv $PWD/coverage.xml $PWD/shippable/codecoverage/
```

Enable the repo sample_go and run it using an Ubuntu minion. Once the
build finishes execution, you can check for the console output, test and
codecoverage results on the respective build's page.

## Java

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

### Testing using Java build tool

#### Maven

- If your repository root has pom.xml file, then our Java builder will use Maven 3 to build it. By default it will run the test using **mvn test**.
- Java builder will execute the below line to install project dependencies with Maven before it starts running tests.

         mvn install -DskipTests=true

#### Gradle

- If your repository root has "build.gradle", then our Java builder will use gradle to build it. By default it will use **gradle check** to run the test.
- Java builder will execute the below line to install the project dependencies with gradle.

         gradle assemble

#### Ant

- If your repository root does not have gradle or maven files, then our Java builder will use Ant to build it. By default it will use **Ant test** to run the test suite.
- Since there is no standard way to install Ant dependencies, you will need to specify the **install:** key to install your project dependencies with Ant.

         language: java
         install: ant deps

Save the test output in shippable/testresults and the codecoverage output in shippable/codecoverage folder to get the reports parsed. If the test and codecoverage output is not saved as specified, you will not find the reports in our CI.

### Build Examples

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

### Multi-module Maven builds

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

## Nodejs

This section helps you to configure the yml file for your node_js
project.

### Node_js versions for testing

- Set the appropriate language and the version number. You can test against multiple versions for a single push by adding more entries. node_js minions use `node_js` by default to set the version.

         # language setting
         language: node_js

         # version numbers
         node_js:
           - "0.11"
           - "0.10"
           - "0.8"
           - "0.6"

    > **Note**
    >
    > We are setting multiple versions of node_js here which means a single
    > push to repo will trigger multiple builds.

### Test scripts

-   To run your test suites using NPM, specify it using script key.

         script: npm test

-   You can also add testing frameworks like Vows, Expresso in the
    package.json file.
-   To test using Vows:

         "scripts": {
             "test": "vows --spec"
         }

-   To test using Expresso:

         "scripts": {
             "test": "expresso test "
         }

-   You can also install your project dependencies using [NPM](http://npmjs.org/)

         node_js:
             - "0.10"
         before_install: npm install -g grunt-cli

-   If you want to build a project with node versions like 0.6, 0.8, 0.10, and 0.11 and want to use the same package.json, add the following line to your yml file, which will upgrade the npm to v.1.4 for node versions 0.6 and 0.8.

         if [[ $SHIPPABLE_NODE_VERSION =~ [0].[6-8] ]]; then npm install -g npm@~1.4.6; fi

-   Keep the output of test and code coverage generated in the Shippable
    folders to get the visualization of your reports.
-   If your test runner uses the junit format, then you can save the
    output in shippable/testresults folder so that shippable can parse
    the test reports. You can also save the output of code coverage in
    shippable/codecoverage folder so that shippable can parse the
    coverage reports.

### Build Examples

These samples will help you get started with Shippable. Test and
Coverage tools used here are [istanbul](https://npmjs.org/package/istanbul) and [mocha](https://npmjs.org/package/mocha).

[Node Sample](https://github.com/shippableSamples/sample_node)

[Node Sample with CouchDB](https://github.com/shippableSamples/sample-node-couchdb)

[Node Sample with MongoDB](https://github.com/shippableSamples/sample_node_mongo)

[Node Sample with MySQL](https://github.com/shippableSamples/sample_node_mysql)

[Node Sample with Neo4j](https://github.com/shippableSamples/sample_node_neo4j)

[Node Sample with Postgres](https://github.com/shippableSamples/sample_node_postgres)

[Node Sample with Protractor](https://github.com/shippableSamples/sample_node_protractor)

[Node Sample with RethinkDB](https://github.com/shippableSamples/sample-node-rethinkdb)

[Node Sample with Selenium](https://github.com/shippableSamples/sample_node_selenium)

[Node Sample with SQLite](https://github.com/shippableSamples/sample_node_sqlite)

Keep the test and code coverage output in the special folders
Shippable/testresults and Shippable/codecoverage to get your reports
parsed.

We need the yml file to analyze the project details. So add the
shippable.yml file to the root of your repo by specifying:

**language :** Specify the language used to create the project. node_js
is used in our project.

**version numbers :** 0.10 is used.

**env:** Specify the xunit output file path using env.

**script:** Specify the command to run tests using script key.

**after_script:** Specify the command to generate code coverage and
save the results in their respective shippable folder. If you have not
created the folders, you can create them using the before_script key.

Here is the complete yml file for sample_node project.

```yaml
# Language setting
    language: node_js

# Version number
    node_js:
      - 0.10.25

# The path for Xunit to output test reports
env:
      - XUNIT_FILE=shippable/testresults/result.xml

# Create directories for test and coverage reports
before_script:
      - mkdir -p shippable/testresults
      - mkdir -p shippable/codecoverage

# Running the tests with grunt
script:
      - grunt

# Tell istanbul to generate a coverage report
after_script:
      - ./node_modules/.bin/istanbul cover grunt -- -u tdd
      - ./node_modules/.bin/istanbul report cobertura --dir  shippable/codecoverage/
```

Enable the repo sample_node and run it using an Ubuntu minion. Once the
build finishes execution, you can check for the console output, test and
codecoverage results on the respective build's page.

## PHP

This section helps you to create a shippable.yml file for your php
project.

- Set the appropriate language and the version number. You can test against multiple versions for a single push by adding more entries. PHP minions use `php` by default to set the runtime platform.

         # language setting
         language: php
         # php tag
         php:
           - 5.3
           - 5.4
           - 5.5
           - 5.6
           - hhvm

### Test scripts

-   Use the script key in shippable.yml file to specify what command to run tests with.

         script: phpunit UnitTest

    > **Note**
    >
    > Runs the tests that are provided by the class UnitTest. This class is
    > expected to be declared in the UnitTest.php sourcefile.

### PHP extensions

Our minions are pre-installed with the following PHP extensions:

1. [amqp.so](http://php.net/amqp)
2. [apc.so](http://php.net/apc)
3. [memcache.so](http://php.net/memcache)
4. [memcached.so](http://php.net/memcached)
5. [mongo.so](http://php.net/mongo)
6. [redis.so](http://pecl.php.net/package/redis)
7. [xdebug.so](http://xdebug.org/)
8. [zmq.so](http://in1.php.net/manual/en/book.zmq.php)

You will have to add **extension=<extension>.so** to php.ini file to
enable the extension. For example, configure your yml file as shown
below to enable redis.so.

```
echo "extension = redis.so" >> ~/.phpenv/versions/$(phpenv version-name)/etc/php.ini
```

It is also possible to install custom PHP extensions using [PECL](http://pecl.php.net/):

```
pecl install <extension>
```

PECL will automatically enable the extension at the end of the
installation.

### Build Examples

These samples will help you get started with Shippable. Test and
Coverage tools used here are [phpunit](http://phpunit.de/).

[PHP Sample](https://github.com/shippableSamples/sample_php)

[PHP Sample with Memcached](https://github.com/shippableSamples/sample_php_memcached)

[PHP Sample with MongoDB](https://github.com/shippableSamples/sample_php_mongo)

[PHP Sample with MySQL](https://github.com/shippableSamples/sample_php_mysql)

[PHP Sample with Nginx](https://github.com/shippableSamples/sample_php_nginx)

[PHP Sample with Redis](https://github.com/shippableSamples/sample_php_redis)

We need the yml file to analyze the project details. So add the
shippable.yml file to the root of your repo by specifying:

**language :** Specify the language used to create the project. php is
used in this sample project.

**php :** Specify the runtime against which your build needs to run
using **php** tag. The sample project uses “5.4".

**script :** Specify the command to run the test and coverage using
script key and export the generated junit test output to
shippable/testresults folder and xml coverage output to
shippable/codecoverage foldet to get the visualization of reports.

**notification alerts:** Email notifications are disabled in this sample
project.

This is the complete yml file for sample_php project:

```yaml
language: php

php:
  - 5.4

before_script:
  - mkdir -p shippable/codecoverage
  - mkdir -p shippable/testresults

script:
  - phpunit --log-junit shippable/testresults/junit.xml --coverage-xml shippable/codecoverage tests/calculator_test.php

notifications:
  email: false
```

Create a project by enabling the repo sample_php and run it using an
Ubuntu minion. Once the build finishes execution, you can check for the
console output, test and code coverage output on the respective build’s
tab.


## Python

This section helps you to configure the yml file for your python
project.

### Choosing Python versions to test against

- Set the appropriate language and the version number. You can test against multiple versions for a single push by adding more entries. Python minions use `python` by default to set the version.

         # language setting
         language: python

         # version numbers
         python:
           - "2.7"
           - "3.2"
           - "3.3"
           - "pypy"

- We support different versions of python like 2.6, 2.7, 3.2, 3.3, 3.4
  and pypy.
- Install dependencies for your project using the **install** key.

         install: "pip install -r requirements.txt --use-mirrors"

-   **Test scripts** : Use the **script** key in the shippable.yml file to specify what command to run tests with.

         # command to run tests
         script: nosetests

-   Test against multiple versions of Django by setting the **env** key and then install the required dependencies for it using the install key.

         env:
           - DJANGO_VERSION=1.2.7
           - DJANGO_VERSION=1.3.7
           - DJANGO_VERSION=1.4.10
           - DJANGO_VERSION=1.5.5
           - DJANGO_VERSION=1.6

         install:
           - pip install -q mock==0.8 Django==$DJANGO_VERSION
           - pip install . --use-mirrors


    > **Note**
    >
    > We are setting multiple versions here which means a single push to
    > repo will trigger multiple builds.

### Build Examples

These samples will help you get started with Shippable. Test and
Coverage tools used here are [nose](https://pypi.python.org/pypi/nose)
and [coverage 3.7](https://pypi.python.org/pypi/coverage/).

[Python Sample](https://github.com/shippableSamples/sample_python)

[Python Sample with MySQL](https://github.com/shippableSamples/sample_python_mysql)

[Python Sample with Postgresql](https://github.com/shippableSamples/sample_python_postgres)

[Python Sample with MongoDB](https://github.com/shippableSamples/sample_python_mongodb)

[Python Sample with CouchDB](https://github.com/shippableSamples/sample-python-couchdb)

[Python Sample with RethinkDB](https://github.com/shippableSamples/sample-python-rethinkdb)

[Python Sample with Neo4j](https://github.com/shippableSamples/sample_python_neo4j)

[Python Sample with Coveralls](https://github.com/shippableSamples/sample_python_coveralls)

[Python Sample with Elasticsearch](https://github.com/shippableSamples/sample_python_elasticsearch)

[Python Sample with Memcache](https://github.com/shippableSamples/sample_python_memcache)

[Python Sample with RabbitMQ](https://github.com/shippableSamples/sample_python_rabbitmq)

[Python Sample with Redis](https://github.com/shippableSamples/sample_python_redis)

[Python Sample with SQLlite](https://github.com/shippableSamples/sample_python_sqllite)

Keep the output generated for test and code coverage in the special
folder Shippable/testresults and Shippable/codecoverage to get parsed
reports parsed and to get a visualization of the reports.The test
results must be generated in Junit format.

We need the yml file to analyze the project details. Add the
shippable.yml file to the root of your repo by specifying:

**language :** Specify the language used to create the project. Python
is used in this sample project.

**version number :** Specify the version numbers against which your
build needs to run. The sample project uses "2.7".

**install :** Install the required dependencies for your repo here.

**script :** Specify the command to run the test and coverage and save
the results to their respective shippable folders. If you have not
created the folders, then you can create it using the before_script
key.

**Notification alerts:** Email notifications are added to get the alerts
about the build status.

Here is the complete yml file for sample_python project.

```yaml
language: python

python:
   - 2.7

install:
   - pip install -r requirements.txt

# Make folders for the reports

before_script:
   - mkdir -p shippable/testresults
   - mkdir -p shippable/codecoverage

script:
   - nosetests test.py --with-xunit --xunit-file=shippable/testresults/nosetests.xml
   - which python && coverage run --branch test.py
   - which python && coverage xml -o shippable/codecoverage/coverage.xml test.py
```

Enable the repo sample_python and run it using an Ubuntu minion. Once
the build finishes execution, you can check for the console output, test
and codecoverage results on the respective build's page.


## Ruby

This section helps you to set the build environment and other
configuration specific to Ruby projects.

### Ruby versions for testing

- Set the appropriate language and version number. You can test against multiple versions for a single push by adding more entries. Ruby minions use `rvm` by default to set the version

         # language setting
         language: ruby

         # version numbers
         rvm:
           - 1.8.7
           - 1.9.2
           - 1.9.3
           - 2.0.0
           - 2.1.0
           - 2.1.1
           - 2.1.2
           - jruby-18mode
           - jruby-19mode
           - rbx
           - ruby-head
           - jruby-head
           - ree

    > **Note**
    >
    > We are setting multiple versions of Ruby here which means a single
    > push to repo will trigger multiple builds.

-  Though we pre-install only a few versions of Ruby, all versions of Ruby are supported. As long as they are available as a binary for Ubuntu 12.04, you can specify custom patchlevels.

         language: ruby
         rvm: 2.0.0-p247

    > **Note**
    >
    > This binds you to potentially unsupported releases of Ruby. It also
    > extends your build time as downloading and installing a custom Ruby
    > can add an extra 60 seconds or more to your build the first time it
    > installs.

    We are using Bundler, `bundle install` to install all your gems. We also
    use `rake` by default to run your build and hence you need to specify it
    in your gemfile

- If you are using a custom gemfile thats not in default location you can specify it with the `gemfile` tag

         # gemfile tag
         gemfile: gemfiles/Gemfile.ci

    > **Note**
    >
    > If you specify multiple gemfiles in the above tag, a matrix build is
    > triggered for every version of the gemfile.

-  Additional arguments can be added to `bundle install` command and we will append them to the default command

         # bundle_args
         bundler_args: --binstubs

- If you want to run some other commands before install you can do this

         # before_install tag
         before_install: gem install bundler --pre

-   You can also set multiple environment variables and test against multiple different versions by using the env variable in your code. This will fire 3 different builds, one for each env variable

         # env tag
         env:
           - CHEF_VERSION=0.9.18
           - CHEF_VERSION=0.10.2
           - CHEF_VERSION=0.10.4

-   You can also test against multiple `jdk` versions

         # jdk tag
         jdk:
           - openjdk7
           - oraclejdk7
           - openjdk6

-   You can also update the versions on your minion by running a simple
    command or even downgrade if you choose to. The script below
    demonstrates an upgrade and downgrade - .. code-block:: python

         before_install:
           - gem update --system
           - gem --version
           - gem update --system 2.1.11
           - gem --version

### Build Examples

These samples will help you get started with Shippable. Test and
Coverage tools used here are [simplecov](http://rubydoc.info/gems/simplecov/) and
[RSpec](http://rspec.info/).

[Ruby Sample](https://github.com/shippableSamples/sample_ruby)

[Ruby Sample with Neo4j](https://github.com/shippableSamples/sample_ruby_neo4j)

[Ruby Sample with Cassandra](https://github.com/shippableSamples/sample_ruby_cassandra)

[Ruby Sample with CouchDB](https://github.com/shippableSamples/sample-ruby-couchdb)

[Ruby Sample with RethinkDB](https://github.com/shippableSamples/sample-ruby-rethinkdb)

[Ruby Sample with RabbitMQ](https://github.com/shippableSamples/sample_ruby_rabbitmq)

We need the yml file to analyze the project details. So add the
shippable.yml file to the root of your repo by specifying the following
details:

**language :** Specify the language used to create the project. Ruby is
used in this sample code.

**version number :** Specify the platform version using **rvm:**. This
sample project uses 1.9.2

**env:** Keep the test and code coverage output in the special folders
Shippable/testresults and Shippable/codecoverage to get parsed reports.

**notification alerts:** Email notifications are added to get alerts
about the build status.

Here is the complete yml file for sample_ruby project:

```yaml
language: ruby
rvm:
  - 1.9.2
env:
  - CI_REPORTS=shippable/testresults COVERAGE_REPORTS=shippable/codecoverage
notifications:
  email:
    - exampleone@org.com
# ensure the test output and coverage dirs are created
before_script:
  - mkdir -p shippable/testresults
# write the rspec tests to the output dir
script:
  - rspec -f JUnit -o shippable/testresults/results.xml
notifications:
 email:
     recipients:
         - exampleone@org.com
         - exampletwo@org.com
```

Enable the repo sample_ruby and run it using an Ubuntu minion. Once the
build finishes execution, you can check for the console output, test and
codecoverage results on the respective build's page.

## Scala

This section helps you specify the build environment and other
configuration specific to Scala projects.

- Set the appropriate language and version number. You can test
  against multiple versions for a single push by adding more entries.
  Scala minions use `scala` by default to set the version.
- We support SBT, oraclejdk8, oraclejdk7, openjdk6 and openjdk7. Specify Scala versions like 2.8.x, 2.9.x and 2.10.x in the shippable.yml file as shown below.

         language: scala
         scala:
           - 2.8.2
           - 2.9.2

-   Scala builder assumes dependency management based on projects like
    Maven, Gradle or SBT and it will pull down project dependencies
    automatically before running tests.
-   To test against multiple JDKs, use jdk tags. For example, to test against the oraclejdk8, oraclejdk7, openjdk6 and openjdk7

         jdk:
           - oraclejdk8
           - oraclejdk7
           - openjdk6
           - openjdk7

### SBT projects

-   If your repository root has **Project** directory or build.sbt file, then our scala builder will run the test suite using

         sbt ++$SHIPPABLE_SCALA_VERSION test

### Build Examples

This sample will help you get started with Shippable. Testing framework
used here is [ScalaTest](http://scalatest.org/).

[Scala Sample](https://github.com/shippableSamples/sample_scala)

[Scala Sample with MongoDB](https://github.com/shippableSamples/sample_scala_mongo)

[Scala Sample with PostgreSQL](https://github.com/shippableSamples/sample_scala_postgres)

Keep the test and code coverage output in the special folders
Shippable/testresults and Shippable/codecoverage to get the reports
parsed. The test report must be in Junit format.

We need the yml file to analyze your project details . So add the
shippable.yml file to the root of your repo by specifying :

**language :** Scala is used in this sample project

**version number :** 2.10.2 is the version used in this sample project.

**notification alerts:** Email notifications are added to get alerts
about the build status.

Here is the complete yml file for sample_scala project

```yaml
language: scala
scala:
  - 2.10.2
```

Enable the repo sample_scala and run it using an Ubuntu minion. Once
the build finishes execution, you can check for the console output, test
and codecoverage results on the respective build's page.

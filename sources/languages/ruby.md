page_title: Ruby | Documentation | Shippable
page_description: Language specific guide for Ruby
page_keywords: containers, lxc, concepts, explanation, image, minion, shippable, Ruby

# Ruby

This section helps you to set the build environment and other
configuration specific to Ruby projects.

## Ruby versions for testing

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

## Build Examples

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

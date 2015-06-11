page_title: Shippable CI Build Flow | Documentation | Shippable
page_description: This section describes how to set up tests and run a build on Shippable
page_keywords: getting started, questions, documentation, shippable, config, yml

# Basic Build Flow: Test and Run Build

## Build Configuration

Your shippable.yml file tells us about your project and how to run your
builds and tests. This file should be at the root of your repository in
order to build the repo with Shippable. Your yml can be as minimal or as
customized as necessary, depending on the project.

To help Travis CI users quickly test our platform, we support
.travis.yml natively, so you will not need shippable.yml if you already have .travis.yml.

At a minimum, Shippable needs to have your language and build version
specified in the yml. We will default to the most common commands if
commands for the other sections are not specified.

The descriptions below are generic to all build environments and all
languages. If you are looking for language specific tags, please refer
to our [language guides](languages/index.html) for more information.

## Build Flow

When we receive a build trigger through a webhook or manual run, we
execute the following steps:

1.  Clone/Pull the project from Github or Bitbucket. This depends on
    whether the minion is in pristine state or not
2.  `cd` into the workspace
3.  Checkout the commit that is being built
4.  Run the `before_install` commands. This is typically used to prep
    your minion and install/update any packages
5.  Run `install` section. This should be used to install any project
    specific libraries or packages
6.  Run `before_script` commands. Create any folders and unzip files
    that might be needed for testing. Some users also restore DBs, copy
    environment variables, etc. here
7.  Run the `script` commands. This runs the build and all your tests
8.  Run either `after_success` or `after_failure` commands, depending on
    the result of your build. after\_success can be used to deploy to
    any supported cloud provider
9.  Run `after_script` command

Build status is determined based on the outcome of the above steps. They
need to return an exit code of `0` to be marked as success. Everything
else is treated as a failure.

Any errors in `after_script` will not affect the status of the build.

## Detailed Steps

### build_image

By default, all Shippable builds use the image shippable/minv2. This is
a huge image and comes pre-installed with all popular language versions,
along with all supported tools and services. If you want to use the
default image, you do not need the `build_image` tag in your yml.

The recommended approach is to use one of our [language specific
images](languages/images.md).

Please note that language specific images do not have any tools and services pre-installed, so you w$

### language

You should specify the programming language and versions of the language
you’d like to build against. You can test against multiple versions with
a single commit by adding more in the versions section.

```yml
# language setting
language: node_js

# version numbers, testing against two versions of node
node_js:
    - 0.10.25
    - 0.11
```

### before_install

The `before_install` section is used to prep your build minion with any
packages or dependencies that are not already pre-installed.

For example, if you require docco and coffee\_script for your Node.js
app, you would do the following -

```yml
# npm install runs by default but shown here for illustrative purposes
before_install:
    - npm install docco
    - npm install coffee-script
```

If nothing is specified in this section, we will attempt to install
dependencies for your app in an idiomatic way for the language (such as
invoking rake for ruby, or pip for python)

### install

The install section lets you install packages and dependencies that are
specific to your project. This is useful if your project uses non
standard tools.

```yml
#install java project dependencies with ant
install: ant deps
```

### script

The `script` section is where the magic happens. In this section, you
an specify the main build commands for your project. Again, if you list
nothing here, your build minion will attempt to make a logical choice
based on your specified language.

```yml
# Running npm test to run your test cases
script:
    - npm test
```

You can run any script file as part of your configuration, as long as it
has a valid shebang command and the right `chmod` permissions.

```yml
# script file
script: ./minions/do_something.sh
```

If you want to prevent shippable from using the default build command
you can add following:

```yml
# script file
script: ./minions/do_something.sh
```

If you want to prevent shippable from using the default build command
you can add following:

```yml
# script file
script:
- true #or any custom command
```

### after_success or after_failure

These sections are used to specify commands to be called after the build
succeeds or fails. For example, for a Java project using Cobertura, this
section can be used to clean up files created during instrumentation.

```yml
after_success:
   - mvn clean cobertura:cobertura
```

Commonly, `after_success` section is also used to add deployment scripts

```yaml
after_success:
    - test -f ~/.ssh/id_rsa.heroku || ssh-keygen -y -f ~/.ssh/id_rsa > ~/.ssh/id_rsa.heroku && herok$
    - git remote -v | grep ^heroku || heroku git:remote --ssh-git --app $APP_NAME
    - git push -f heroku master
```

### after_script

This is the last user defined section to be executed, and can be used to
perform tasks after the build and tests are complete, like generating a
coverage report -

```yml
# Tell istanbul to generate a coverage report
 after_script:
     - ./node_modules/.bin/istanbul cover grunt -- -u tdd
     - ./node_modules/.bin/istanbul report cobertura --dir  shippable/codecoverage/
```




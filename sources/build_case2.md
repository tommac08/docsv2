page_title: How to run a build with test and coverage reports on Shippable | Documentation | Shippable
page_description: Run a build with test and coverage reports on Shippable CI
page_keywords: how-to, questions, documentation, shippable, Continuous Integration, Continuous Deployment, CI/CD

# Run a build with Test Reports and Coverage Visualization

This guide will walk you through the basic sections of the YML as well project settings you need to run a build with test reports and coverage visualization.

This assumes you have already signed into Shippable through Github or Bitbucket. Read our [Getting Started page](index.md) if this is your first time to Shippable.

You will learn how to -

- Use some of the basic YML Tags (_language_, _before\_install_, _install_, _script_, _before\_script_, _after\_script_)
- Choose an appropriate build image for your project
- Turn on Test reports and Coverage Visualization
- Enable your project and run a build
- Download Artifacts

*****

## Sample Project

We will walk through this guide with a [sample nodejs project](https://github.com/shippableSamples/sample_ubuntu1204_nodejs). This is a public project, so you can fork it to your repo and update the yml as you walk through the guide.

Read more about other languages in our [how-to guide on languages](languages.md)

*****

## Create your YML file

This section helps you to configure the yml file for your node_js project.

### language and version
Set the appropriate language and the version number. You can test against multiple versions for a single push by adding more entries. node_js minions use `node_js` by default to set the version.

```
# language setting
language: node_js

# version numbers
node_js:
  - "0.10"
  - "0.11"
```

**Note:**We are setting multiple versions of node_js here which means a single push to repo will trigger multiple builds, which we refer to as a Build Matrix.

### before_install

The `before_install` section is used to prep your build minion with any packages or dependencies that are not already pre-installed.

```
before_install:
# Activate the required node.js version. $SHIPPABLE_NODE_VERSION for this sample is 0.10.24
  - source ~/.nvm/nvm.sh && nvm install $SHIPPABLE_NODE_VERSION
  - node --version
  - npm install -g grunt-cli
```

If nothing is specified in this section, we will attempt to install dependencies for your app in an idiomatic way for the language (such as invoking rake for ruby, or pip for python)

### install

The install section lets you install packages and dependencies that are specific to your project. This is useful if your project uses non standard tools.

```
# npm is installed by default. shown here for illustrative purposes
install:
  -npm install
```

### script

The `script` section is where the magic happens. In this section, you can specify the main build commands for your project. Again, if you list nothing here, your build minion will attempt to make a logical choice based on your specified language. In our case, we use grunt to run our tests. So our script section looks like below.

```
# Running npm test to run your test cases
script:
    - grunt
```

You can run any script file as part of your configuration, as long as it
has a valid shebang command and the right `chmod` permissions.

```
# script file
script: ./minions/do_something.sh
```

If you want to prevent shippable from using the default build command you can add following:

```
# script file
script: ./minions/do_something.sh
```

or

```
# script file
script:
- true #or any custom command
```

*****

### Test and Coverage Reports

Test and Coverage tools used in the sample are [istanbul](https://npmjs.org/package/istanbul) and [mocha](https://npmjs.org/package/mocha).

To use Shippable's test visualization feature,
 - The test and code coverage output has to be in special folders Shippable/testresults and Shippable/codecoverage
 - The test results must be in JUNIT format and the code coverage output needs to be in cobertura xml format


#### env

Specify the xunit output file path using env.

```
# The path for Xunit to output test reports
env:
  - XUNIT_FILE=shippable/testresults/result.xml
```

#### before_script and after_script

Specify the command to generate code coverage and save the results in their respective shippable folder in the `after_script` section. If you have not created the folders, you can create them using the `before_script` key.

after_script is the last user defined section to be executed and can be used to perform any tasks after the build and run tests are complete (like generating coverage report)

```
# Create directories for test and coverage reports
before_script:
      - mkdir -p shippable/testresults
      - mkdir -p shippable/codecoverage

# Tell istanbul to generate a coverage report
 after_script:
     - ./node_modules/.bin/istanbul cover grunt -- -u tdd
     - ./node_modules/.bin/istanbul report cobertura --dir  shippable/codecoverage/
```

*****

### Complete YML for Sample Project

Here is the complete yml file for sample_node project.

```
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

*****

## Build Image in Project Settings

Go to your **Project Page**, click on **Settings** and choose the following image:

`Pull Image from : shippableimages/ubuntu1204_nodejs`

This is the image used for the sample project.

You can find other build images [here](https://github.com/shippableImages/)

*****

## Enable the repo

- On the Shippable Landing page, click on `CI`
- Find your subscription on the dropdown
- If this is your first time, click on (+) to enable a new project
- On the New Projects page, click the `Enable` (key) Icon

*****

## Run the build

Builds can be triggered through webhooks or manually through shippable.com.

**Manual Builds:** After enabling the project, click the **Run Build** button to
manually run a build. It will redirect you to the build's page where you can see the streaming console logs.

**Webhooks:** Our webhooks are triggered when a commit is pushed to your repo, or if a
pull request is created. Webhooks are a good way to verify that commits to your project build in a clean environment, and not just on the committer's machine.

*****

## Check output

Once the build finishes execution, you can check for the console output, test and
code coverage results on the respective build's page.

**Console Log**: Stdout of a build run is streamed to the browser in
real-time using websockets. In addition, there are other important
pieces of information like

-   build status
-   duration
-   GitHub changeset id
-   committer info

**Test cases**: Test run output is streamed in real-time to the console
log when the tests are executed. Since we have added test reports to our project, the build engine will automatically parse it after the build completes and the results will appear in the Tests tab.

**Code Coverage**: The Coverage tab shows a visual of the coverage results from `./shippablecodecoverage`.

**Script Tab:** The Script tab shows you the details of the build script and the environment variables used for your build

*****

## Useful Additions

### Artifacts

To enable build artifacts to be archived, set the following flag in your yml.

```
archive: true
```

All files in the ./shippable folder at the root of the project are automatically archived for each run upon completion.
You can download a tarball of your build's artifacts by clicking on the download archive button on the build page.


### after_success or after_failure

These yml sections are used to specify commands to be called after the build
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

page_title: nodejs | Documentation | Shippable
page_description: language specific guide for node_js
page_keywords: nodejs, mocha, npm, node

# Nodejs

This section helps you to configure the yml file for your node_js
project.

## Node_js versions for testing

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

## Test scripts

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

## Build Examples

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


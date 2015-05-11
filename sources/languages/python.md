page_title: python
page_description: Configuring yml file for python
page_keywords: Python, pip, nosetests, mirrors

# Python

This section helps you to configure the yml file for your python
project.

## Choosing Python versions to test against

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

## Build Examples

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



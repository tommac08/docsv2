# Google App Engine

Google App Engine supports Python, PHP, Go and Java applications.
Support for PHP is in preview, while for Go is marked as experimental.
As runtime present on App Engine is a very specific one, with many
Google-specific services and some blacklisted modules, it is recommended
to use Google App Engine SDK both during the development and testing of
your application.

## Installation of the GAE SDK

SDKs for all the runtimes are available as ZIP downloads on [the Google
App Engine page](https://developers.google.com/appengine/downloads). The
SDK contains tools to interact with the GAE API: for instance, it allows
deployment of the application. Moreover, it comes with Development
Server that lets you test the application on your local machine (and on
Shippable minion), simulating the GAE environment. Stubs for the GAE
services are also provided to make unit testing easier.

Download the SDK for your platform from the link above to begin working
on the application. To make the SDK available for your Shippable build
(here, for a Python project), add the following `before_install` step:

```yaml
env:
  global:
    - GAE_DIR=/tmp/gae

before_install:
  - >
    test -e $GAE_DIR ||
    (mkdir -p $GAE_DIR &&
     wget https://storage.googleapis.com/appengine-sdks/featured/google_appengine_1.9.6.zip -q -O /tmp/gae.zip &&
     unzip /tmp/gae.zip -d $GAE_DIR)
```

It will first test if the tools are already available and download &
unzip them if there are not.

## Using Datastore from Python

Google App Engine offers a number of storage services. One of them is
NDB Datastore that is instantly available to your application, once you
deploy it to the platform. To interact with Datastore, you need to use
libraries bundled with the SDK. Below is a simple example of code that
stores and retrieves data from Datastore. More information can be found
in [the GAE documentation](https://developers.google.com/appengine/docs/python/ndb/):

```python
from google.appengine.ext import ndb

class Score(ndb.Model):
  score = ndb.IntegerProperty()
  timestamp = ndb.DateTimeProperty(auto_now_add=True)

class Storage():
  def score_key(self):
    return ndb.Key('Score', 'Store')

  def populate(self):
    new_score = Score(parent=self.score_key())
    new_score.score = random.randint(1, 1234)
    new_score.put()

  def get_score(self):
    score_query = Score.query(ancestor=self.score_key()).order(-Score.timestamp)
    return score_query.get().score
```

No connection setup is required, as the GAE will handle providing the
service to your application automatically. Thanks to the existence of
the Development Server, we can test this code both with a unit test and
a integration test.

Unit test that stubs Datastore calls looks as follows:

```python
import unittest
from google.appengine.ext import db
from google.appengine.ext import testbed
from helloworld import Storage

class HelloTestCase(unittest.TestCase):
  def setUp(self):
    self.testbed = testbed.Testbed()
    self.testbed.activate()
    self.testbed.init_datastore_v3_stub()

  def tearDown(self):
    self.testbed.deactivate()

  def test(self):
    storage = Storage()
    storage.populate()
    score = storage.get_score()
    self.assertLess(score, 1234)

if __name__ == "__main__":
  unittest.main()
```

The only GAE-specific code is enclosed in `setUp` and `tearDown` methods
and it initializes and then closes the stubbing framework.

We can also write an integration test, in which the code connects to a
mock database included in the SDK:

```python
from webtest import TestApp
from helloworld import application

app = TestApp(application)

def test_index():
  response = app.get('/')
  assert 'Hello, World' in response
```

For the above to work, we need to use a dedicated test runner that will
run the test in the Development Server environment. For example, to use
[NoseGAE](https://github.com/Trii/NoseGAE), we need to install the
following modules (preferably listed in `requirements.txt` file):

```bash
nose
coverage
NoseGAE
WebTest
```

We then install them on Shippable minion, using the following `install`
step:

```bash
install:
  - pip install -r requirements.txt
```

Finally, we can launch the both tests by invoking the test runner with
extra arguments during the `script` step:

```bash
script:
  - >
    nosetests test.py func_test.py
    --with-gae --without-sandbox --gae-lib-root=$GAE_DIR/google_appengine
    --with-xunit --xunit-file=shippable/testresults/test.xml
    --with-coverage --cover-xml --cover-xml-file=shippable/codecoverage/coverage.xml
```

Please note the second line of the command, where we turn on the GAE
plugin and pass the path of the SDK installation on the minion. The
`--without-sandbox` option was required to have the tests working
successfully. This NoseGAE option tries to simulate the GAE environment,
where some functions are prohibited. Apparently, it doesn't work
correctly for Datastore services.

The other parameters are here to generate JUnit XML report in the
location expected by Shippable, as well as the coverage report.

## Deployment of a Python application

After you create the application using the GAE Admin Console, you can
deploy it using the `appcfg` tool from the SDK. First, create `app.yaml`
file, including your application name in the first line:

```bash
application: sample-python-datastore
version: 1
runtime: python27
api_version: 1
threadsafe: 1

handlers:
- url: /.*
  script: helloworld.application
```

Then, we need to authenticate against the GAE API. We have two options
here that are suitable for non-interactive build environment:
password-based authentication and OAuth2.

To setup password-based authentication, include two environment
variables in your `shippable.yml`: `EMAIL` (that stores the name of your
Google account) and `GAE_PASSWORD`. It is recommended to store the
password in encrypted form, using secure_env_variables:

```bash
env:
  global:
    - GAE_DIR=/tmp/gae
    - EMAIL=shippable@gmail.com
    - secure: lffPR8giDdKinq1LfjTabgM8Lufb3sdweFWJcoU8o/KIvwTg9NOxEw3oG5pw4+pI0c3q/k0JkBv7QgDGkoiRHwZkebWYNcHwyo2NFaa/cpwpNjv3pMZsXpMiw+duSvfjA/XmFAynmW8/ft2YaAzpB1Mbn5p2k7ID2qCMv/YmFgIu605VK/WUnYPEdxMD2vkifVSNAIH42GOR+2ht4nKj85Wsu9OGgMBJ5XAqVcQoWX+Ui9yZvtaf3WKzowg+MC4PQ0qGLH/l6WHkY8bBCduMz65JjZIss2s972L4P8Hwpk+gDdVtRE82hKH7GuEYdNKhKjbthZmn5AF4thI72N5TjQ==
```

Next, you can invoke `appcfg update` command to deploy new version of
your application with the following `after_success` step:

```yaml
after_success:
  - echo "$GAE_PASSWORD" | $GAE_DIR/google_appengine/appcfg.py -e "$EMAIL" --passin update .
```

> **Note**
>
> If you use two-factor authentication for your Google account, you need
> to generate application-specific password for the GAE to use. Refer to
> [this documentation](https://support.google.com/accounts/answer/185833?hl=en)
> for the details.

Alternatively, you can use OAuth2 protocol to authenticate against the
GAE API. To set it up, first run this command in the repository root on
your local workstation:

```bash
$PATH_TO_GAE_SDK/appcfg.py --oauth2 list_versions .
```

It will open a page in your browser where you can authorize the GAE to
access your Google account. As the result, `.appcfg_oauth2_tokens` file
will be created in your home directory, containing the access token. You
can then encrypt it as Shippable secure variable and use in your
`after_success` step as follows:

```bash
after_success:
  - $GAE_DIR/google_appengine/appcfg.py --oauth2_access_token=$GAE_TOKEN update .
```

> **Note**
>
> Recently, Google opened a preview of git-based deployment workflow, in
> which you push the code to a git repository, triggering the build. As
> this functionality is not yet in its final form, it is not discussed
> here. Please refer to [the GAE documentation](https://developers.google.com/cloud/devtools/repo/push-to-deploy)
> to track its progress.

Full sample of Python+Datastore application can be found on [our Github
account](https://github.com/shippableSamples/sample-python-datastore-appengine).

## Using Cloud SQL from Python

Another storage service offered by Google App Engine is Cloud SQL. It is
essentially a managed MySQL database and many applications that already
use MySQL (or other relational database) can be ported to Google App
Engine with relatively small effort. For this reason, the examples below
will show how to adapt an existing Django application to use Cloud SQL.

No changes need to be done to the application code. The code that
interacts with the database uses only standard Django ORM abstractions:

```python
from django.db import models

class Score(models.Model):
  score = models.IntegerField()
  timestamp = models.DateTimeField(auto_now_add=True)

class Storage():
  def populate(self):
    new_score = Score()
    new_score.score = random.randint(1, 1234)
    new_score.save()

  def get_score(self):
    score_query = Score.objects.all().order_by('-timestamp')[:1]
    return score_query[0].score
```

Connection settings in `settings.py` are different for each environment.
Details of this configuration are explained in detail below:

```python
APP_ENGINE = os.getenv('SERVER_SOFTWARE', '').startswith('Google App Engine')

if APP_ENGINE:
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.mysql',
          'HOST': '/cloudsql/fifth-composite-657:test',
          'NAME': 'django_test',
          'USER': 'root',
      }
  }
elif os.getenv('SETTINGS_MODE') == 'prod':
    # Running in development, but want to access the Google Cloud SQL instance
    # in production.
    DATABASES = {
        'default': {
            'ENGINE': 'google.appengine.ext.django.backends.rdbms',
            'INSTANCE': 'fifth-composite-657:test',
            'NAME': 'django_test',
            'USER': 'root',
        }
    }
else:
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.mysql',
          'NAME': 'test',
          'USER': 'shippable',
      }
  }
```

Here, the `APP_ENGINE` variable is used to determine whether the
application is running on the App Engine. See [GAE documentation](https://developers.google.com/appengine/docs/python/cloud-sql/django#development-settings)
for details. If so, we use standard MySQL Django engine to communicate
with the database. The `HOST` variable identifies the Cloud SQL
instance. Its value should always have the
`/cloudsql/<application_id>:<instance_id>` format.

The `root` user is configured by Cloud SQL, while the database needs to
be created manually. At the time of writing, (somewhat surprisingly)
this was not possible from the new Google Cloud Developers Console. To
create a database, one needs to go to [old Google APIs console](https://code.google.com/apis/console), choose the correct
project and then switch to "Google Cloud SQL" by clicking the link in
the sidebar. On the right, the "SQL Prompt" tab should be visible that
allows to execute DDL commands (like `create database django_test;`).

The second `if` branch in the configuration above is for connecting to
your Cloud SQL instance via HTTP API. This is particularly useful for
executing management commands against the database, e.g. schema
migrations. The `ENGINE` here uses module provided by Google App Engine
SDK for Python. SDKs for all the runtimes are available as ZIP downloads
on [the Google App Engine page](https://developers.google.com/appengine/downloads).

Instead of `HOST` variable, `INSTANCE` is passed here directly. The
other options are the same as for the 'production' configuration. Using
this configuration, we can update schema on the Cloud SQL instance (or
initialize it just after database creation), with the following
commands:

```bash
export PYTHONPATH=$PYTHONPATH:$GAE_DIR/google_appengine/lib/django-1.5:$GAE_DIR/google_appengine
SETTINGS_MODE='prod' python ./manage.py syncdb
```

Here, we assume that `GAE_DIR` is the location where you unpacked the
Google App Engine SDK. By setting `SETTINGS_MODE` environment variable
we are triggering use of the second configuration.

The third configuration is only used for testing and development
purposes and it uses database installed locally. For simplicity, we use
here `test` as database name and `shippable` as the user, so the
settings on the developer workstation will mirror the ones found on
Shippable minion. Of course, you can add another configuration section
to keep settings for Shippable and development environment separate. We
create the database on the Shippable minion in the `before_script` step:

```yaml
before_script:
  - mkdir -p shippable/testresults
  - mkdir -p shippable/codecoverage
  - mysql -e 'create database test;'
```

Finally, we need to declare dependencies on the libraries that we will
use for connecting to the database and testing the storage-related
classes. In the `requirements.txt` file, we add the following modules:

```bash
coverage
Django==1.5
django-nose
mock
mock-django
MySQL-python
```

Then, during the build, we install these dependencies in `install` step:

```yaml
install:
  - pip install -r requirements.txt
```

Google App Engine uses different mechanism for dependency management. To
ensure any non-standard modules will be available to your application,
you need to add `libraries` section in the `app.yaml` file that is read
by GAE deployment tools:

```yaml
libraries:
- name: django
  version: "1.5"
- name: MySQLdb
  version: "latest"
```

See the section below on deploying Django applications for the details.

Again, unit tests for the application using Cloud SQL do not contain any
GAE-specific code:

```python
import unittest
from mock import patch, Mock
from mock_django.query import QuerySetMock
from django_cloudsql.storage import Storage
from models import Score

class StorageTestCase(unittest.TestCase):
  @patch('django_cloudsql.storage.Score')
  def test(self, score_class_mock):
    save_mock = Mock(return_value=None)
    score_class_mock.return_value.save = save_mock

    storage = Storage()
    storage.populate()
    self.assertEqual(save_mock.call_count, 1)

    score = Score()
    score.score = 1234
    score_class_mock.objects.all.return_value.order_by.return_value = QuerySetMock(score_class_mock, score)
    score = storage.get_score()
    self.assertEqual(score, 1234)

if __name__ == "__main__":
  unittest.main()
```

Running tests within the Django application context can be performed
using `django-nose` test runner. To prevent it from being loaded on the
production environment, we can once again use the `APP_ENGINE` variable:

```python
APP_ENGINE = os.getenv('SERVER_SOFTWARE', '').startswith('Google App Engine')

# Application definition

INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'django_cloudsql',
)

# Add nose test runner if not on the production
if not APP_ENGINE:
  INSTALLED_APPS = INSTALLED_APPS + ('django_nose',)

TEST_RUNNER = 'django_nose.NoseTestSuiteRunner'

NOSE_ARGS = [
  '--with-xunit', '--xunit-file=shippable/testresults/test.xml',
  '--with-coverage', '--cover-xml', '--cover-xml-file=shippable/codecoverage/coverage.xml',
]
```

As all the paths needed to generate test and coverage reports consumed
by Shippable are passed in the `settings.py`, we can then run the tests
with the following `script` build step:

```bash
script:
  - python manage.py test
```

See the sample of Django+Cloud SQL application on [our GitHub account](https://github.com/shippableSamples/sample-django-cloudsql-appengine) for the details.

## Deployment of a Django application

After you create the application using the GAE Admin Console, you can
deploy it using the `appcfg` tool from the SDK. First, create `app.yaml`
file, including your application name in the first line (please note the
declaration of the dependencies):

```bash
application: fifth-composite-657
version: 1
runtime: python27
api_version: 1
threadsafe: 1

libraries:
- name: django
  version: "1.5"
- name: MySQLdb
  version: "latest"

builtins:
- django_wsgi: on
```

Then, we need to authenticate against the GAE API. Please refer to
gae_python_deployment for details on different methods of
authentication. Full example of `shippable.yml` file, including download
of the SDK and deployment of the application in the `after_success` step
would then look like follows:

```yaml
language: python
python:
  - 2.7

env:
  global:
    - GAE_DIR=/tmp/gae
    - EMAIL=shippable@gmail.com
    - secure: lffPR8giDdKinq1LfjTabgM8Lufb3sdweFWJcoU8o/KIvwTg9NOxEw3oG5pw4+pI0c3q/k0JkBv7QgDGkoiRHwZkebWYNcHwyo2NFaa/cpwpNjv3pMZsXpMiw+duSvfjA/XmFAynmW8/ft2YaAzpB1Mbn5p2k7ID2qCMv/YmFgIu605VK/WUnYPEdxMD2vkifVSNAIH42GOR+2ht4nKj85Wsu9OGgMBJ5XAqVcQoWX+Ui9yZvtaf3WKzowg+MC4PQ0qGLH/l6WHkY8bBCduMz65JjZIss2s972L4P8Hwpk+gDdVtRE82hKH7GuEYdNKhKjbthZmn5AF4thI72N5TjQ==

before_install:
  - >
    test -e $GAE_DIR ||
    (mkdir -p $GAE_DIR &&
     wget https://storage.googleapis.com/appengine-sdks/featured/google_appengine_1.9.6.zip -q -O /tmp/gae.zip &&
     unzip /tmp/gae.zip -d $GAE_DIR)

install:
  - pip install -r requirements.txt

before_script:
  - mkdir -p shippable/testresults
  - mkdir -p shippable/codecoverage
  - mysql -e 'create database test;'

script:
  - python manage.py test

after_success:
  - echo "$GAE_PASSWORD" | $GAE_DIR/google_appengine/appcfg.py -e "$EMAIL" --passin update .
```

Full sample of Django+Cloud SQL application can be found on [our GitHub account](https://github.com/shippableSamples/sample-django-cloudsql-appengine).

## Using Datastore from Go

To interact with Datastore from Go, you need to use libraries bundled
with the SDK. Below is a simple example of code that stores and
retrieves data from Datastore. More information can be found in [the GAE
documentation](https://developers.google.com/appengine/docs/go/datastore/):

```go
import (
  "math/rand"
  "time"

  "appengine"
  "appengine/datastore"
)

type Score struct {
  Score int
  Date  time.Time
}

func scoreKey(c appengine.Context) *datastore.Key {
  return datastore.NewKey(c, "Scores", "default_scoreboard", 0, nil)
}

func populate(c appengine.Context) error {
  score := Score{
    Score: rand.Intn(1234),
    Date:  time.Now(),
  }
  key := datastore.NewIncompleteKey(c, "Score", scoreKey(c))
  _, err := datastore.Put(c, key, &score)
  return err
}

func getScore(c appengine.Context) (int, error) {
  query := datastore.NewQuery("Score").Ancestor(scoreKey(c)).Order("-Date").Limit(1)
  for t := query.Run(c); ; {
    var score Score
    if _, err := t.Next(&score); err != nil {
      return -1, err
    }

    return score.Score, nil
  }
}
```

No connection setup is required, as the GAE will handle providing the
service to your application automatically.

Unit test that stubs Datastore calls using [aetest package](https://godoc.org/code.google.com/p/appengine-go/appengine/aetest) looks as follows:

```go
import (
  "testing"

  "appengine/aetest"
)

func TestStorage(t *testing.T) {
  c, err := aetest.NewContext(nil)
  if err != nil {
    t.Fatal(err)
  }
  defer c.Close()

  if err := populate(c); err != nil {
    t.Fatal(err)
  }

  score, err := getScore(c)
  if err != nil {
    t.Fatal(err)
  }
  if score < 0 || score > 1023 {
    t.Errorf("Score outside of expected range: %d", score)
  }
}
```

> **Note**
>
> Full integration testing of GAE Go applications with automatic mocking
> of the services is not yet available, but work on it is [being performed by Google team](https://groups.google.com/d/msg/google-appengine-go/9JZDLUMRkRE/B_UOS44UQjkJ).

For the above to work, we need to run the tests via `goapp` command that
is supplied as part of the GAE Go SDK. Its installation and setup is
described in the section below.

## Deployment of a Go application

Go packages are resolved relative to `GOPATH` variable that needs to be
set both in your development environment and on Shippable minion. [The common practice](http://code.google.com/p/go-wiki/wiki/GithubCodeLayout) when structuring an application that is hosted on GitHub is to name your packages according to the following pattern:

```bash
github.com/<GitHub username>/<repository name>/<package name, probably nested>
```

For example, the package that houses the main HTTP handler in [our Go sample](https://github.com/shippableSamples/sample-go-datastore-appengine)
is called `github.com/Shippable/sample-go-datastore-appengine/hello`. It
follows that in your development environment the contents of the sample
repository would be stored in the
`$GOPATH/src/github.com/Shippable/sample-go-datastore-appengine` path.

Adhering to this convention ensures that the testing tools (which are
package-aware) will work correctly and that your package can be consumed
by other packages.

Google App Engine slightly diverges from this structure, expecting to
find the main entry point for the application in the root of your
repository. In other words, while `goapp test` command lives in a
package-oriented world, `goapp serve` and `goapp deploy` are tied to the
current directory. Hence, it is common to create a dispatcher in the
root of the repository that then calls the individual packages:

```go
package routes

import (
  "net/http"

  "github.com/Shippable/sample-go-datastore-appengine/hello"
)

func init() {
  http.HandleFunc("/", hello.Handler)
}
```

This way, we can test the individual packages, while ensuring that the
application will deploy properly. The following snippet from
`shippable.yml` downloads the GAE Go SDK, installs the packages required
for generation of test and coverage reports and then links the
repository to the correct place in Go workspace (root of which is
identified by `GOPATH`). Of note are also the environment variables used
to authenticate against the GAE API. Please refer to
gae_python_deployment for details on different methods of
authentication.

```yaml
env:
  global:
    - GAE_DIR=/tmp/go_appengine
    - EMAIL=shippable@gmail.com
    - secure: lffPR8giDdKinq1LfjTabgM8Lufb3sdweFWJcoU8o/KIvwTg9NOxEw3oG5pw4+pI0c3q/k0JkBv7QgDGkoiRHwZkebWYNcHwyo2NFaa/cpwpNjv3pMZsXpMiw+duSvfjA/XmFAynmW8/ft2YaAzpB1Mbn5p2k7ID2qCMv/YmFgIu605VK/WUnYPEdxMD2vkifVSNAIH42GOR+2ht4nKj85Wsu9OGgMBJ5XAqVcQoWX+Ui9yZvtaf3WKzowg+MC4PQ0qGLH/l6WHkY8bBCduMz65JjZIss2s972L4P8Hwpk+gDdVtRE82hKH7GuEYdNKhKjbthZmn5AF4thI72N5TjQ==

before_install:
  - >
    test -e $GAE_DIR ||
    (mkdir -p $GAE_DIR &&
     wget https://storage.googleapis.com/appengine-sdks/featured/go_appengine_sdk_linux_amd64-1.9.6.zip -q -O /tmp/gae.zip &&
     unzip /tmp/gae.zip -d /tmp)
  - go get github.com/jstemmer/go-junit-report
  - go get github.com/t-yuki/gocover-cobertura
  - mkdir -p $GOPATH/src/github.com/Shippable
  - ln -sfn $PWD $GOPATH/src/github.com/Shippable/sample-go-datastore-appengine
```

Finally, we can launch the test by invoking the test runner with extra
arguments during the `script` step:

```yaml
script:
  - >
    $GAE_DIR/goapp test -v -coverprofile=shippable/codecoverage/coverage.out github.com/Shippable/sample-go-datastore-appengine/hello |
      $GOPATH/bin/go-junit-report > shippable/testresults/results.xml
  - $GOPATH/bin/gocover-cobertura < shippable/codecoverage/coverage.out > shippable/codecoverage/coverage.xml
```

Please note that we use `goapp` command from the GAE SDK instead of the
standard `go` command. This is required in order to be able to use
`aetest` package.

Next, we need to create the application using the GAE console and create
`app.yml` file with matching application name:

```yaml
application: sample-go-datastore
version: 1
runtime: go
api_version: go1

handlers:
- url: /.*
  script: _go_app
```

Finally, we can deploy the application to Google App Engine in
`after_success` step:

```yaml
after_success:
  - echo "$GAE_PASSWORD" | $GAE_DIR/appcfg.py -e "$EMAIL" --passin update .
```

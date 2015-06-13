page_title: Deploying to Heroku| Documentation | Shippable
page_description: How to deploy your application to Heroku
page_keywords: heroku, continuous deployment, CI/CD

# Heroku

Heroku supports Ruby, Java, Python, Node.js, PHP, Clojure and Scala
(with special support for Play Framework), so you can use these
technologies to build and deploy apps on Heroku. There are two methods
of deploying your applications to Heroku: using Heroku toolbelt or plain
git command only.

## Without Heroku toolbelt

To be able to push your code to Heroku, you need to add SSH public key
associated with your Shippable account to authorized keys in [Heroku
Account Settings](https://dashboard.heroku.com/account). You can find the Shippable Deployment Key [here](ci_settings/#deployment-key).
Add the key to the 'SSH keys' section of Heroku settings.

Next, create your Heroku application using Web GUI or `heroku` command
installed on your workstation.

- Go to your app's settings page.
- In the application info pane (that is also displayed at the end of
  the application creation process) you will see 'Git URL'.
- Just use it to push the code in `after_success` step of Shippable build:

```yml
env:
  global:
    - APP_NAME=shroudd-headland-1758

after_success :
  - git push -f git@heroku.com:$APP_NAME.git master
```

Full sample of deploying PHP+MySQL application to Heroku without using
toolbelt can be found on [our GitHub account](https://github.com/shippableSamples/sample-php-mysql-heroku/tree/without-toolbelt).

> **Note**
>
> If you happen to build other branches than `master`, please see
> [Deploying from branches other than master](#heroku_other_branches) for details.

## With Heroku toolbelt

In some circumstances, you may choose to use Heroku toolbelt to deploy
your application. For example, you may want to execute Rake commands
straight from your Shippable build (like `heroku run rake db:migrate`).

To use Heroku toolbelt, you first need to obtain API key for your
account. Go to your [account settings](https://dashboard.heroku.com/account) and copy it from 'API Key' section. It is recommended to save your access key as secret in
Shippable, as is discussed in secure_env_variables. Encrypt your
variable as `HEROKU_API_KEY=<your key here>` and paste the encrypted
secret in `shippable.yml` as follows:

```yaml
env:
  global:
    - APP_NAME=shroudd-headland-1758
    - secure: MRuHkLbL9HPkJPU5lzkKM1+NOq1S5RrhxEyhJkk60xxYiF7DMzydiBN8oFBjWrSmyGeGRuEC22a0I5ItobdWVszfcJCaXHwtfKzfGOUdKuyCnDgvojXhv/jrBvULyLK6zsLw3b8NMxdnwNsHqSPm19qW/EIGEl9Zv/637Igos69z9aT7+xrEG013+6HtKYb8RHm+iPSNsFoBi/RSAHYuM1eLTZWG2WAkjgzZaYmrHCgNwVmk+HOGR+TOWN7Iu5lrjyvC1XDCQrOvo1hZI30cd9OqJ5aadFm3exQpNhI4I7AgOnCbK3NoWNc/GAnqKXCvsaIQ80Jd/uLIOVyMjD6Xmg==
```

> **Note**
>
> If your build times out during `after_success` step, please double
> check that you correctly defined `HEROKU_API_KEY` variable. If no key
> is supplied, Heroku toolbelt will switch to an interactive mode,
> prompting for the username and causing the build to 'hang'.

Then, install the toolbelt in `before_install` step (`which heroku` is
for skipping this step if the tools are already installed):

```yaml
before_install:
  - which heroku || wget -qO- https://toolbelt.heroku.com/install-ubuntu.sh | sh
```

Next, create your Heroku application using Web GUI or `heroku` command
installed on your workstation. Then, add the following `after_success`
step to your Shippable build:

```yaml
after_success:
  - test -f ~/.ssh/id_rsa.heroku || ssh-keygen -y -f ~/.ssh/id_rsa > ~/.ssh/id_rsa.heroku && heroku keys:add ~/.ssh/id_rsa.heroku
  - git remote -v | grep ^heroku || heroku git:remote --ssh-git --app $APP_NAME
  - git push -f heroku master
```

- First we generate public SSH key out of the private one to a file
  with a custom name. We then authorize this key with Heroku. Using
  custom name for the file allows us to skip this step on subsequent
  builds. Please note that we need to use `test -f ...` instead of
  `[ -f ... ]` here, as the latter would be interpreted by YAML parser
- Then, we make sure `heroku` remote is added to the local git
  repository
- Finally, we push the code to Heroku

Please refer to the sections below for language-specific details of
configuring Heroku builds.

> **Note**
>
> If you happen to build other branches than `master`, please see
> [Deploying from branches other than master](#heroku_other_branches) for details.

## Deploying from branches other than master

Heroku always deploys contents of `master` branch, so if you happen to
deploy code from other branches, [the Heroku documentation](https://devcenter.heroku.com/articles/git#deploying-code) instructs you to use the following syntax:

```yaml
- git push -f heroku yourbranch:master
```

During the build, you can access the name of the branch as `BRANCH`
variable, so the invocation would look as follows. We are forcing push
here, as it can happen that builds (and pushes) can alternate between
the branches, so plain push would fail due to divergent histories.

```yaml
- git push -f heroku $BRANCH:master
```

## Using ClearDB MySQL database

Heroku passes ClearDB MySQL connection details as an environment
variable called `CLEARDB_DATABASE_URL` containing connection URL. To
mock it with the test database during build, add the following
environment variable in your `shippable.yml` config:

```yaml
env:
  global:
    - CLEARDB_DATABASE_URL=mysql://shippable@127.0.0.1:3306/test?reconnect=true
```

Then, in your application you need to retrieve and parse the url. For
example, in PHP:

```php
$url = parse_url(getenv("CLEARDB_DATABASE_URL"));
$host = $url["host"];
$username = $url["user"];
$password = array_key_exists("pass", $url) ? $url["pass"] : "";
$db = substr($url["path"], 1);

$con = mysqli_connect($host, $username, $password, $db);
```

Please refer to [Heroku docs](https://devcenter.heroku.com/articles/cleardb) for details on how to fetch and parse the url in different programming languages. Full
sample of deploying PHP+MySQL application to Heroku (using Heroku
toolbelt) can be found on [our GitHub account](https://github.com/shippableSamples/sample-php-mysql-heroku).

## Using Heroku Postgres with Ruby on Rails

Configuring Ruby on Rails application to work with Postgres on Heroku is
really simple, thanks to Heroku doing all heavy-lifting related to
setting up the connection. When Heroku detects that the application you
deploy is using Ruby on Rails, it will overwrite `config/database.yml`
file with correct production configuration.

On Shippable, Postgres in version 9.3 is started by default during
minion boot. To use different version of Postgres, please refer to the
dedicated section on PostgreSQL configuration.

All we need to do is to create a database in the `before_script` step:

```yaml
before_script:
  - mkdir -p shippable/testresults
  - mkdir -p shippable/codecoverage
  - psql -c 'create database "sample-rubyonrails-postgres-heroku_test";' -U postgres
```

And then include its name in `config/database.yml` file that is stored
in the repository (username and password do not need to be configured):

```yaml
test:
  <<: *default
database: sample-rubyonrails-postgres-heroku_test
```

The last thing to do is to add `pg` to your `Gemfile`. Note that it will
be done automatically if you create your rails app with
`--database=postgresql` option. See [our sample Ruby on Rails Heroku
application](https://github.com/shippableSamples/sample-rubyonrails-postgres-heroku)
for details.

## Test and coverage reports for Ruby on Rails

In Rails 4 and Ruby 1.9+, the built-in test framework is based on
Minitest. To enable Shippable-compatible reporting of test and coverage
reports, we need to add the following gems to the `Gemfile`:

```bash
gem 'simplecov'
gem 'simplecov-csv'
gem 'minitest-reporters'
```

Then, add the following snippet at the beginning of the
`test/test_helper.rb` file:

```bash
require 'minitest/reporters'
require 'simplecov'
require 'simplecov-csv'
SimpleCov.formatter = SimpleCov::Formatter::CSVFormatter
SimpleCov.coverage_dir(ENV["COVERAGE_REPORTS"])
SimpleCov.start

MiniTest::Reporters.use! [MiniTest::Reporters::DefaultReporter.new,
                          MiniTest::Reporters::JUnitReporter.new(ENV["CI_REPORTS"])]
```

Finally, we need to add environment variables with the locations for the
reporter results:

```yaml
env:
  global:
    - CI_REPORTS=shippable/testresults COVERAGE_REPORTS=shippable/codecoverage
```

See [our sample Ruby on Rails Heroku application](https://github.com/shippableSamples/sample-rubyonrails-postgres-heroku) for details.

## General information on using MongoDB

You have two addons to choose from when using MongoDB on Heroku:
MongoLab and MongoHQ. Setup for both of them with Shippable is the same.
The only difference is the name of the environment variable that
contains connection details:

- For MongoLab it is called `MONGOLAB_URI`
- For MongoHQ the name of the variable is `MONGOHQ_URL`

Examples below use MongoLab for consistency, but adapting them to
MongoHQ is as simple as substituting all occurrences of this variable.

To start using MongoDB, first add the addon of your choice to your
Heroku application.

In your `shippable.yml` you first need to tell Shippable to provide your
build with MongoDB service. Then, provide mock connection URL to be used
by your tests. On Shippable, MongoDB is accessed without providing user
nor password.

```yaml
services:
   - mongodb

env:
  global:
    - APP_NAME=rocky-wave-3011
    - MONGOLAB_URI=mongodb://localhost/test
```

Then proceed to configure your application as is outlined in
per-language guides below.

## Using MongoDB with PHP

First, activate the official Mongo driver extension in `php.ini` on
Shippable minion, as is explained in the documentation on PHP
extensions:

```yaml
before_script:
  - mkdir -p shippable/testresults
  - mkdir -p shippable/codecoverage
  - echo "extension=mongo.so" >> ~/.phpenv/versions/$(phpenv version-name)/etc/php.ini
```

Then, tell Heroku to enable the extension as well by providing the
following `composer.json` file in the root of your repository:

```json
{
  "require": {
    "ext-mongo": "*"
  }
}
```

Finally, you can connect to the database with the code as follows:

```php
$mongoUrl = getenv("MONGOLAB_URI");
$dbName = substr(parse_url($mongoUrl)["path"], 1);
$this->mongo = new Mongo($mongoUrl);
$this->scores = $this->mongo->{$dbName}->scores;
```

Please note that we need to parse the URL to the instance to extract the
database name, as the Mongo driver expects that the database is selected
by accessing property named the same as the database, which is
demonstrated in the last line of the snippet above.

Full sample of deploying PHP+MongoDB application to Heroku (using Heroku
toolbelt) can be found on [our GitHub account](https://github.com/shippableSamples/sample-php-mongo-heroku).

## Using MongoDB with Python

First, create file called `Procfile` that will tell Heroku how to launch
your Python application. For example, if you use Flask and create the
application under name `application` in `hello.py`

```yaml
web: gunicorn hello:application
```

Next, declare that your application depends on MongoDB official driver.
Heroku requires `requirements.txt` file in the root of your repository
that may be generated with `pip freeze` command. For our (Flask)
example, we will use file with contents as follows:

```bash
nose
coverage
gunicorn
Flask
pymongo
```

Finally, you can connect to the database with the following code:

```python
mongo_url = os.environ['MONGOLAB_URI']
db_name = urlparse.urlparse(mongo_url).path[1:]
client = MongoClient(mongo_url)
self.db = client[db_name]
```

Please note that we need to parse the URL to the instance to extract the
database name, as the Python Mongo driver follows the convention of
accessing the database by property with the same as the database, which
is demonstrated in the last line of the snippet above.

Full sample of deploying Python+MongoDB application to Heroku (using
Heroku toolbelt) can be found on [our GitHub account](https://github.com/shippableSamples/sample-python-mongo-heroku).

## Using MongoDB with Ruby

First, create a file called `Procfile` that will tell Heroku how to
launch your application. For example, if you use Sinatra and your
application entry point is located in file called `helloworld.rb`:

```yaml
web: bundle exec ruby helloworld.rb -p $PORT
```

Next, declare your dependencies in `Gemfile`. For example, if using
[Mongoid](http://mongoid.org/) to access the database:

```bash
source "https://rubygems.org"

gem "rspec"
gem "simplecov"
gem "simplecov-csv"
gem "rspec_junit_formatter"
gem "sinatra"
gem "mongoid"
```

To separate Mongoid configuration from your code, create YAML file (e.g.
called `mongoid.yml`):

```yaml
production:
  sessions:
    default:
      uri: <%= ENV['MONGOLAB_URI'] %>
```

Next, use this file to connect to the database in your application:

```ruby
require 'mongoid'
Mongoid.load!('mongoid.yml', :production)
```

You can also execute Rake tasks in your `after_success` step using
Heroku toolbelt. For example, to run database migrations at the end of
the build:

```yaml
after_success:
  - test -f ~/.ssh/id_rsa.heroku || ssh-keygen -y -f ~/.ssh/id_rsa > ~/.ssh/id_rsa.heroku && heroku keys:add ~/.ssh/id_rsa.heroku
  - git remote -v | grep ^heroku || heroku git:remote --ssh-git --app $APP_NAME
  - git push -f heroku $BRANCH:master
  - heroku run rake db:migrate
```

Full sample of deploying Sinatra+MongoDB application to Heroku (using
Heroku toolbelt) can be found on [our GitHub account](https://github.com/shippableSamples/sample-ruby-mongo-heroku).

## Using MongoDB with Node.js

First, create file called `Procfile` that will tell Heroku how to launch
your Node.js application. For example, if you use Express and define
your routes in file called `app.js`:

```yaml
web: node app.js
```

Next, declare that your application depends on Mongoose (or other
library of your choice). Heroku will read your `package.json` file:

```json
...
"dependencies": {
  "express": "~4.2.0",
  "mongoose": "^3.8.12",
  "when": "~3.2.3"
},
```

Finally, you can connect to the database with the following code:

```javascript
var mongoose = require('mongoose');
mongoose.connect(process.env.MONGOLAB_URI);
```

Full sample of deploying Express+MongoDB application to Heroku (using
Heroku toolbelt) can be found on [our GitHub account](https://github.com/shippableSamples/sample-nodejs-mongo-heroku).

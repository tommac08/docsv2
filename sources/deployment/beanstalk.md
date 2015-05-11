# Amazon Elastic Beanstalk

Amazon Elastic Beanstalk features predefined runtime environments for
Java, Node.js, PHP, Python and Ruby, so it is possible to configure
Shippable minions to automatically deploy applications targeting these
environments. Moreover, Elastic Beanstalk also support defining custom
runtime environments via Docker containers, giving the developer full
flexibility in the configuration of technology stack. However, as the
standard, pre-packaged environments are far easier to set up and cover
nearly all languages currently supported by Shippable (except for Go),
we will concentrate on how to integrate with Amazon Elastic Beanstalk
using the former option.

To interact with Elastic Beanstalk, one needs to use command line tools
supplied by Amazon, from which the most commonly used is `eb` command.
These tools must be available to Shippable in order to perform
deployment. The easiest way of doing so is to install them in
`before_install` step:

```yaml
before_install:
  - SUDO=$(which sudo) && $SUDO pip install awsebcli
```

You also need to obtain Access Key to connect `eb` tool with Elastic
Beanstalk API. Please refer to [this documentation](http://docs.aws.amazon.com/general/latest/gr/getting-aws-sec-creds.html) for details on obtaining the keys. It is recommended to save your access key as secret in Shippable, as is discussed in secure_env_variables.
To use code from this tutorial, store the secret access key variable as
`AWSSecretKey`. It is safe to keep your access key id in plain text.

After having the basic setup done, it is time to create an application
in Elastic Beanstalk. You can use Web GUI for this task, by going to the
main page in the Elastic Beanstalk console, and then choosing 'Create a
New Application' button in the sidebar. After entering name for
application, proceed to define an environment. When you have the
environment ready, create a file in your repository called `config.yml`
with your settings:

```
branch-defaults:
  master:
    environment: sample-nodejs-env
global:
  application_name: sample-nodejs
  default_region: us-west-2
  profile: eb-cli
  sc: git
```

> **Note**
>
> You can also create this file interactively, by running `eb init`
> command on your workstation and making a copy or checking into the
> repository the file it creates (`.elasticbeanstalk/config.yml`).

In the runtime environment, RDS database connection details are injected
by Elastic Beanstalk as environment variables. It is secure and
convenient, as we do not need to store them in any other place. However,
during tests on Shippable, we need to supply the same variables with
values correct for Shippable minion in `shippable.yml` (please note the
`secure` definition for our AWS access key):

```yaml
env:
  global:
    - RDS_HOSTNAME=127.0.0.1 RDS_USERNAME=shippable RDS_PASSWORD="" RDS_DB_NAME=test RDS_PORT=3306
    - AWSAccessKeyId=AKIAJJL2U6T3F3Y5JIGA
    - secure: K7qw2XSFBaW+zEzrs0ODKMQq/Bo9AZqGotCXc50fao+et6WaxEmedlK//MO9JozmPdcxDRq5k8A0pmjTLsMLstkh7PUFLu3Z6xowU2OhMyjQ0pS2J8Hw16SgZ9n2EW+3cps4dIijEzOwjA0Yx5rTOC7F9N8nvr/1l4Yp4i11qgW08cefEKuwiF/ypgrkK5BYJyJreZOEJt3lJ6/aXyXxPPl3X3Z+L+ca9mQmTN6q1wnlEcYLDU5EJtkk87KtOfVyoi/+aOFh49eDpwStSD4zDnygia8eAnCGK/p0XGFJCAwWK1nnFY7aklJrvElD+V/2lbr14TwF0rhmiba6Y6ylnw==
```

You can see above that we included AWS keys, with the secret key being
stored as an encrypted variable. Remember to replace both values with
the ones downloaded from your AWS Management Console. Below, you can see
that we copy these keys to the `.aws/config` file, which is used by `eb`
tool to read the credentials. The first line outputted to the file marks
it as `eb-cli` profile, which is the default name used by the `eb` tool.
You can use multiple profiles, choosing the correct one in your
`config.yml` file (see above).

Then, we need to add some steps to `shippable.yml` to update `eb`
configuration and then launch it after successful build.

```yaml
before_script:
  - mkdir -p ~/.aws
  - echo '[profile eb-cli]' > ~/.aws/config
  - echo "aws_access_key_id = $AWSAccessKeyId" >> ~/.aws/config
  - echo "aws_secret_access_key = $AWSSecretKey" >> ~/.aws/config

script:
  - mkdir -p .elasticbeanstalk
  - cp config.yml .elasticbeanstalk/

after_success :
  - eb init && eb deploy
```

Finally, we can connect to the database using environment variables as
defined above.

## PHP

```
$con = mysqli_connect(
  $_SERVER['RDS_HOSTNAME'],
  $_SERVER['RDS_USERNAME'],
  $_SERVER['RDS_PASSWORD'],
  $_SERVER['RDS_DB_NAME'],
  $_SERVER['RDS_PORT']
);
```

Elastic Beanstalk serves your repository root as the document root of
the webserver, so e.g. `index.php` file will be interpreted when you
access the root context of your Elastic Beanstalk application. See full
sample PHP code using Elastic Beanstalk available [on GitHub](https://github.com/shippableSamples/sample-php-mysql-beanstalk).

## Ruby

```
con = Mysql2::Client.new(
  :host => ENV['RDS_HOSTNAME'],
  :username => ENV['RDS_USERNAME'],
  :password => ENV['RDS_PASSWORD'],
  :port => ENV['RDS_PORT'],
  :database => ENV['RDS_DB_NAME']
)
```

Elastic Beanstalk runtime expects that the entry point of your
application will be found in `config.ru` file. See [Amazon documentation](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_Ruby_sinatra.html)
for details. Full sample Ruby code using Sinatra and MySQL on Elastic
Beanstalk is available [on GitHub](https://github.com/shippableSamples/sample-ruby-mysql-beanstalk).

## Python

```
self.db = MySQLdb.connect(
  host = os.environ['RDS_HOSTNAME'],
  user = os.environ['RDS_USERNAME'],
  passwd = os.environ['RDS_PASSWORD'],
  port = int(os.environ['RDS_PORT']),
  db = os.environ['RDS_DB_NAME'])
```

Elastic Beanstalk runtime expects that the entry point of your
application will be found in `application.py` file. See [Amazon documentation](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_Python_flask.html)
for details. Full sample Python code using Flask and MySQL on Elastic
Beanstalk is available [on GitHub](https://github.com/shippableSamples/sample-python-mysql-beanstalk).

## Node.js

```
var connection = mysql.createConnection({
  host: process.env.RDS_HOSTNAME,
  port: process.env.RDS_PORT,
  user: process.env.RDS_USERNAME,
  password: process.env.RDS_PASSWORD,
  database: process.env.RDS_DB_NAME
});
```

Elastic Beanstalk expects that the entry point of your application will
be found in app.js or server.js file in the repository root. See [Amazon documentation](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_nodejs_express.html)
for details. Full sample Node.js code using Express and MySQL on Elastic
Beanstalk is available [on GitHub](https://github.com/shippableSamples/sample-nodejs-mysql-beanstalk).

## Java

For JVM, the connection setting are passed as system properties, rather
than environment variables:

```
private final String dbName = System.getProperty("RDS_DB_NAME");
private final String userName = System.getProperty("RDS_USERNAME");
private final String password = System.getProperty("RDS_PASSWORD");
private final String hostname = System.getProperty("RDS_HOSTNAME");
private final int port = Integer.parseInt(System.getProperty("RDS_PORT"));
private final String databaseUrl = "jdbc:mysql://" + hostname + ":" + port + "/" + dbName;
```

Because of this difference, dummy connection details for Shippable
environment need to be passed as arguments during JVM invocation. Here
is an example for Maven:

```
script:
  - mkdir -p .elasticbeanstalk
  - cp config.yml .elasticbeanstalk/
  - mvn clean cobertura:cobertura
  - mvn test -DRDS_PORT=3306 -DRDS_DB_NAME=test -DRDS_HOSTNAME=localhost -DRDS_PASSWORD= -DRDS_USERNAME=shippable
```

Finally, Elastic Beanstalk [expects exploded WAR](https://forums.aws.amazon.com/thread.jspa?messageID=329550) in the root of the repository, so we need to copy and commit its contents as the final build step, prior to the deployment:

```
after_success:
  - mvn compile war:exploded
  - cp -r target/App/* ./
  - git add -f META-INF WEB-INF
  - git commit -m "Deploy"
  - eb init && eb deploy
```

See the full sample of Java web application featuring MySQL connection
[on GitHub](https://github.com/shippableSamples/sample-java-mysql-beanstalk)
for details.

## Scala

Scala deployment is very similar to the one described for Java above.
First, RDS connection details need to be obtained from system
properties, rather then environment variables. Here is an example for
[Slick](http://slick.typesafe.com/):

```
val dbName = System.getProperty("RDS_DB_NAME")
val userName = System.getProperty("RDS_USERNAME")
val password = System.getProperty("RDS_PASSWORD")
val hostname = System.getProperty("RDS_HOSTNAME")
val port = Integer.parseInt(System.getProperty("RDS_PORT"))
val databaseUrl = s"jdbc:mysql://${hostname}:${port}/${dbName}"

def connect = Database.forURL(
  url = databaseUrl, user = userName, password = password, driver = "com.mysql.jdbc.Driver")
```

Because of this difference, dummy connection details for Shippable
environment need to be passed as arguments during JVM invocation. Here
is an example for `sbt` (please note copying the coverage results, as
[sbt-scoverage](https://github.com/scoverage/sbt-scoverage) does not
allow customizing the path via options):

```
script:
  - mkdir -p .elasticbeanstalk
  - cp config.yml .elasticbeanstalk/
  - sbt -DRDS_PORT=3306 -DRDS_DB_NAME=test -DRDS_HOSTNAME=localhost -DRDS_PASSWORD= -DRDS_USERNAME=shippable scoverage:test
  - cp target/scala-2.10/coverage-report/cobertura.xml shippable/codecoverage/coverage.xml
```

Finally, Elastic Beanstalk [expects exploded
WAR](https://forums.aws.amazon.com/thread.jspa?messageID=329550) in the
root of the repository, so we need to copy and commit its contents as
the final build step, prior to the deployment:

```
after_success:
  - sbt package
  - unzip "target/scala-2.10/*.war" -d ./
  - git add -f META-INF WEB-INF
  - git commit -m "Deploy"
  - eb init && eb deploy
```

See the full sample of Scalatra+Slick web application featuring MySQL
connection [on GitHub](https://github.com/Shippable/sample-scala-mysql-beanstalk) for
details.

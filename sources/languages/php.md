page_title: php | Documentation | Shippable
page_description: How to configure shippable.yml file for php
page_keywords: yml,php,composer,phpunit

# PHP

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

## Test scripts

-   Use the script key in shippable.yml file to specify what command to run tests with.

         script: phpunit UnitTest

    > **Note**
    >
    > Runs the tests that are provided by the class UnitTest. This class is
    > expected to be declared in the UnitTest.php sourcefile.

## PHP extensions

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

## Build Examples

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


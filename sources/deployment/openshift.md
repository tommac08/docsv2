page_title: Deploying to Openshift | Documentation | Shippable
page_description: How to deploy your application on openshift
page_keywords: red hat openshift, continuous deployment, CI/CD

# Red Hat OpenShift

Red Hat OpenShift supports wide variety of runtime environments (called
'cartridges' here). With Java, it is possible to deploy both to JBoss
EAP, JBoss AP / Wildfly, Tomcat and Vert.x. Other supported technologies
include PHP, Ruby, Node.js, Python, Perl.

As the availability of JBoss is quite unique amongst the PaaSes, we will
cover deployment of a Java EE 6 application below.

Preferred build tool for Java applications deployed to OpenShift is
Apache Maven. OpenShift invokes Maven build goal (`package`) as the part
of the deployment, so (contrary to some other platforms) it is not
necessary to upload the build artifacts from Shippable.

After creating the application in the OpenShift web administration panel
(or using the `rhc` command-line tool), we are supplied with git
endpoint that should be used to push the code during the deployment. We
then can declare it as Shippable environment variable and add the remote
in `before_install` step (or skip if it already exists):

```yaml
env:
  global:
    - OPENSHIFT_REPO=ssh://53c851465973ca84e5000597@javamysql-shippablesamples.rhcloud.com/~/git/javamysql.git

before_install:
  - git remote -v | grep ^openshift || git remote add openshift $OPENSHIFT_REPO
```

You also need to give Shippable permissions to deploy to the repository.
It can be easily done by copying your deployment key from Shippable
admin panel and adding it in the 'Public Keys' section of [OpenShift administration panel](https://openshift.redhat.com/app/console/settings).

After this, deployment is as simple, as pushing to the OpenShift
repository in `after_success` step:

```yaml
after_success:
  - git push -f openshift $BRANCH:master
```

> **Note**
>
> Please see heroku_other_branches for explanation of why `BRANCH`
> variable is used above.

## Testing with Arquillian

Suggested testing method for Java EE applications is to use Arquillian.
As opposed to most testing frameworks that rely on unit testing the code
outside of the application container (mostly for speed reasons),
Arquillian performs full-stack testing using the actual server.

In order to use it on Shippable, we first need to download and extract
the version of JBoss Application Server (or Wildfly) that matches the
version used by OpenShift:

```yaml
env:
  global:
    - JBOSS_HOME=/tmp/jboss-as-7.1.0.Final
    - JBOSS_SERVER_LOCATION=http://download.jboss.org/jbossas/7.1/jboss-as-7.1.0.Final/jboss-as-7.1.0.Final.tar.gz
    - OPENSHIFT_REPO=ssh://53c851465973ca84e5000597@javamysql-shippablesamples.rhcloud.com/~/git/javamysql.git

before_install:
  - if [ ! -e $JBOSS_HOME ]; then curl -s $JBOSS_SERVER_LOCATION | tar zx -C /tmp; fi
  - git remote -v | grep ^openshift || git remote add openshift $OPENSHIFT_REPO
```

Then, we need to modify `pom.xml` to tell the Maven plugins to put the
test and coverage results in the directories expected by Shippable:

```xml
<plugins>
  <!-- other plugins omitted for brevity -->
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
      <reportsDirectory>shippable/testresults</reportsDirectory>
    </configuration>
    <dependencies>
      <dependency>
        <groupId>org.apache.maven.surefire</groupId>
        <artifactId>surefire-junit4</artifactId>
        <version>2.7.2</version>
      </dependency>
    </dependencies>
  </plugin>
</plugins>
```

We also add `arq-jbossas-managed` Maven profile that executes the tests
in JBoss container controlled by Arquillian:

```xml
<profiles>
  <profile>
    <!-- An optional Arquillian testing profile that executes tests
      in your JBoss AS instance -->
    <!-- This profile will start a new JBoss AS instance, and execute
      the test, shutting it down when done -->
    <!-- Run with: mvn clean test -Parq-jbossas-managed -->
    <id>arq-jbossas-managed</id>
    <dependencies>
      <dependency>
        <groupId>org.jboss.as</groupId>
        <artifactId>jboss-as-arquillian-container-managed</artifactId>
        <version>${jboss.version}</version>
        <scope>test</scope>
      </dependency>
    </dependencies>
  </profile>
</profiles>
```

Finally, we can launch the tests in the `script` step (please recall
that we don't need to build the actual EAR file, as it is being
automatically built by OpenShift during the deployment):

```bash
before_script:
  - mkdir -p shippable/testresults
  - mkdir -p shippable/codecoverage

script:
  - mvn clean cobertura:cobertura
  - mvn test -Parq-jbossas-managed
```

Please refer to the [application sample](https://github.com/shippableSamples/sample-java-mysql-openshift) for an example Arquillian test of a RESTful webservice.

## Using MySQL

When initializing the application with `rhc` command-line tool or using
any of the quickstart repositories provided by OpenShift, a special
`.openshift` directory gets created in the repository root. It can
contain various hooks into the deployment process and configuration
files that will be copied into JBoss AS instance of the cartridge.

To connect to MySQL, we will use the datasource that is already defined
in the `.openshift/config/standalone.xml.as7`:

```xml
<datasource jndi-name="java:jboss/datasources/MySQLDS" enabled="${mysql.enabled}" use-java-context="true" pool-name="MySQLDS" use-ccm="true">
  <connection-url>jdbc:mysql://${env.OPENSHIFT_MYSQL_DB_HOST}:${env.OPENSHIFT_MYSQL_DB_PORT}/${env.OPENSHIFT_APP_NAME}</connection-url>
  <driver>mysql</driver>
  <security>
    <user-name>${env.OPENSHIFT_MYSQL_DB_USERNAME}</user-name>
    <password>${env.OPENSHIFT_MYSQL_DB_PASSWORD}</password>
  </security>
  <validation>
    <check-valid-connection-sql>SELECT 1</check-valid-connection-sql>
    <background-validation>true</background-validation>
    <background-validation-millis>60000</background-validation-millis>
    <!--<validate-on-match>true</validate-on-match>-->
  </validation>
  <pool>
    <flush-strategy>IdleConnections</flush-strategy>
  </pool>
</datasource>

<drivers>
  <driver name="mysql" module="com.mysql.jdbc">
    <xa-datasource-class>com.mysql.jdbc.jdbc2.optional.MysqlXADataSource</xa-datasource-class>
  </driver>
</drivers>
```

> **Note**
>
> When defining your own datasource, please check that you use correct
> module name (`com.mysql.jdbc`) for the MySQL driver. Many examples on
> the Internet use `com.mysql`, but the configuration file in `modules`
> directory of OpenShift JBoss server uses this convention.

You don't need to include the driver in your deployment package, as it
will be already provided by OpenShift.

Then, you need to use this datasource in your `persistence.xml`
configuration file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.0"
    xmlns="http://java.sun.com/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
    http://java.sun.com/xml/ns/persistence
    http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd">
  <persistence-unit name="primary">
    <jta-data-source>java:jboss/datasources/MySQLDS</jta-data-source>
    <properties>
      <!-- Properties for Hibernate -->
      <property name="hibernate.dialect" value="org.hibernate.dialect.MySQLDialect" />
      <property name="hibernate.hbm2ddl.auto" value="create-drop" />
      <property name="hibernate.show_sql" value="false" />
    </properties>
  </persistence-unit>
</persistence>
```

If you would like to test with different datasource (for example, with
in-memory H2 database), you can override this configuration by providing
different persistence configuration:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.0"
   xmlns="http://java.sun.com/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="
        http://java.sun.com/xml/ns/persistence
        http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd">
   <persistence-unit name="primary">
      <jta-data-source>java:jboss/datasources/TestDS</jta-data-source>
      <properties>
         <!-- Properties for Hibernate -->
         <property name="hibernate.hbm2ddl.auto" value="create-drop" />
         <property name="hibernate.show_sql" value="false" />
      </properties>
   </persistence-unit>
</persistence>
```

Then, include it in your Arquillian test archive as `persistence.xml`:

```java
return ShrinkWrap.create(WebArchive.class, "test.war")
  .addClasses(Score.class, ScoreRestService.class, JaxRsActivator.class, Resources.class)
  .addAsResource("META-INF/test-persistence.xml", "META-INF/persistence.xml")
```

We invite you to explore our JavaEE+MySQL sample for OpenShift on the
[Shippable GitHub account](https://github.com/shippableSamples/sample-java-mysql-openshift).

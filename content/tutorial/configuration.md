---
title: 'Configuration'
weight: 10
---

## Configuration

Let's start by creating a new Apache Maven project. We'll need a POM with the
proper dependencies. We expect you to be familiar with Apache Maven, so we'll
just show you a working POM file:

**pom.xml**

```xml
<?xml version="1.0"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>li.strolch</groupId>
    <artifactId>strolch-bookshop</artifactId>
    <version>0.1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <name>strolch-bookshop</name>
    <description>Bookshop built on Strolch</description>
    <inceptionYear>2017</inceptionYear>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.build.timestamp.format>yyyy-MM-dd HH:mm:ss</maven.build.timestamp.format>
        <buildTimestamp>${maven.build.timestamp}</buildTimestamp>

        <jdk.version>1.8</jdk.version>

        <jersey.version>2.25.1</jersey.version>
        <slf4j.version>1.7.25</slf4j.version>
        <logback.version>1.2.3</logback.version>
        <petitparser.version>2.1.0</petitparser.version>
        <hikaricp.version>4.0.3</hikaricp.version>
        <postgresql.version>42.1.4</postgresql.version>
        <gson.version>2.8.2</gson.version>
        <annotation.version>1.3.1</annotation.version>
        <javaxmail.version>1.6.0</javaxmail.version>
        <serverlet.version>3.1.0</serverlet.version>
        <jaxrs.api.version>2.1</jaxrs.api.version>

        <junit.version>4.12</junit.version>
        <hamcrest.version>1.3</hamcrest.version>
        <mockito.version>2.0.8-beta</mockito.version>

        <maven-compiler-plugin.version>3.7.0</maven-compiler-plugin.version>
        <maven-source-plugin.version>3.0.1</maven-source-plugin.version>
        <maven-jar-plugin.version>3.0.2</maven-jar-plugin.version>
        <maven-war-plugin.version>3.1.0</maven-war-plugin.version>

        <strolch.version>1.8.5</strolch.version>

        <warFinalName>bookshop</warFinalName>
        <m2eclipse.wtp.contextRoot>${warFinalName}</m2eclipse.wtp.contextRoot>
    </properties>

    <dependencies>

        <!-- base -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>${logback.version}</version>
            <scope>runtime</scope>
        </dependency>

        <!-- strolch -->
        <dependency>
            <groupId>li.strolch</groupId>
            <artifactId>li.strolch.utils</artifactId>
            <version>${strolch.version}</version>
        </dependency>
        <dependency>
            <groupId>li.strolch</groupId>
            <artifactId>li.strolch.privilege</artifactId>
            <version>${strolch.version}</version>
        </dependency>
        <dependency>
            <groupId>li.strolch</groupId>
            <artifactId>li.strolch.model</artifactId>
            <version>${strolch.version}</version>
        </dependency>
        <dependency>
            <groupId>li.strolch</groupId>
            <artifactId>li.strolch.agent</artifactId>
            <version>${strolch.version}</version>
        </dependency>
        <dependency>
            <groupId>li.strolch</groupId>
            <artifactId>li.strolch.rest</artifactId>
            <version>${strolch.version}</version>
        </dependency>
        <dependency>
            <groupId>li.strolch</groupId>
            <artifactId>li.strolch.service</artifactId>
            <version>${strolch.version}</version>
        </dependency>
        <dependency>
            <groupId>li.strolch</groupId>
            <artifactId>li.strolch.testbase</artifactId>
            <version>${strolch.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- utils -->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>${gson.version}</version>
        </dependency>

        <!-- web -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>${serverlet.version}</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.ws.rs</groupId>
            <artifactId>javax.ws.rs-api</artifactId>
            <version>${jaxrs.api.version}</version>
        </dependency>
        <dependency>
            <groupId>org.glassfish.jersey.core</groupId>
            <artifactId>jersey-common</artifactId>
            <version>${jersey.version}</version>
        </dependency>
        <dependency>
            <groupId>org.glassfish.jersey.core</groupId>
            <artifactId>jersey-server</artifactId>
            <version>${jersey.version}</version>
        </dependency>
        <dependency>
            <groupId>org.glassfish.jersey.containers</groupId>
            <artifactId>jersey-container-servlet</artifactId>
            <version>${jersey.version}</version>
        </dependency>

        <!-- testing -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
            <version>${hamcrest.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-library</artifactId>
            <version>${hamcrest.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <resources>
            <!-- filter properties files, and copy the rest -->
            <resource>
                <directory>src/main/resources</directory>
                <filtering>true</filtering>
                <includes>
                    <include>**/*.properties</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <filtering>false</filtering>
                <excludes>
                    <exclude>**/*.properties</exclude>
                </excludes>
            </resource>
        </resources>

        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${maven-compiler-plugin.version}</version>
                <configuration>
                    <source>${jdk.version}</source>
                    <target>${jdk.version}</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>${maven-war-plugin.version}</version>
                <configuration>
                    <failOnMissingWebXml>false</failOnMissingWebXml>
                    <warName>${warFinalName}</warName>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <profiles>
        <!-- active when building on eitch's machines -->
        <profile>
            <id>m2e.eitchpc</id>
            <activation>
                <property>
                    <name>user.name</name>
                    <value>eitch</value>
                </property>
                <os>
                    <family>unix</family>
                </os>
            </activation>
            <properties>
                <strolch.env>dev.eitchpc</strolch.env>
            </properties>
        </profile>
    </profiles>
</project>
```

Now we need the rest of the directory structure:

```text
../strolch-bookshop/
   - src/main/java/
     - li/strolch/bookshop/
       - <!-- java classes -->
     - src/main/resources/
       - ENV.properties
       - appVersion.properties
       - logback.xml
     - src/main/webapp/WEB-INF/
       - StrolchBootstrap.xml
   - runtime
     - config/
       - PrivilegeConfig.xml
       - PrivilegeRoles.xml
       - PrivilegeUsers.xml
       - StrolchConfiguration.xml
       - StrolchPolicies.xml
     - data/
       - StrolchModel.xsd
       - defaultModel.xml
       - templates.xml
     - temp/
```

A few notes to the resource files:

* The `ENV.properties` file is filtered by maven and the environment to load is
  written in it using the environment variable strolch.env.
* The `appVersion.properties` file is also filtered by maven and allows to reflect
  on the version of this app at runtime.
* The `logback.xml` file configures logging using SLF4j and Logback.

The `StrolchBootstrap.xml` file is used to configure Strolch's environment and
root directory. For a webapp it can be annoying to store Strolch's configuration
inside the webapp, which is why we can define an absolute path where the
configuration is kept. In the following example we keep it in the root of the
sources:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<StrolchBootstrap>
  <env id="dev.eitchpc" default="true">
    <root>/home/eitch/src/git/strolch-bookshop/runtime</root>
    <environment>dev</environment>
  </env>
</StrolchBootstrap>
```

Here we define two environments, but the both redefine the environment to dev.
This is because we want this app to start on two different machines with
different user home directories. See the profiles in the POM as to how these
environments are activated using a environment property strolch.env.

In this next step we'll create Strolch's configuration at the location we
defined in the StrolchBootstrap.xml file. Strolch's configuration contains of
three directories: config, data and temp. config contains static files which
usually aren't changed, data contains model files in XML format and temp is used
at runtime for any temporary files, e.g. storing active sessions.

The configuration as well as the model has been described on Strolch's
documentation web page, we'll just provide you with the files for the bookshop:

**PrivilegeConfig.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Privilege>
  <Container>
    <Parameters>
      <!-- parameters for the container itself -->
      <Parameter name="secretKey" value="45f251ce-d51f-4624-990a-8dcd5b181f0e"/>
      <Parameter name="secretSalt" value="4770a32d-1512-4891-9a63-362504932500"/>
      <Parameter name="persistSessions" value="true"/>
      <Parameter name="autoPersistOnUserChangesData" value="false"/>
      <Parameter name="privilegeConflictResolution" value="MERGE"/>
    </Parameters>
    <EncryptionHandler class="li.strolch.privilege.handler.DefaultEncryptionHandler">
      <Parameters>
        <Parameter name="hashAlgorithm" value="PBKDF2WithHmacSHA512"/>
        <Parameter name="hashIterations" value="10000"/>
        <Parameter name="hashKeyLength" value="256"/>
      </Parameters>
    </EncryptionHandler>
    <PersistenceHandler class="li.strolch.privilege.handler.XmlPersistenceHandler">
      <Parameters>
        <Parameter name="usersXmlFile" value="PrivilegeUsers.xml"/>
        <Parameter name="rolesXmlFile" value="PrivilegeRoles.xml"/>
      </Parameters>
    </PersistenceHandler>
    <UserChallengeHandler class="li.strolch.privilege.handler.MailUserChallengeHandler">
    </UserChallengeHandler>
  </Container>
  <Policies>
    <Policy name="DefaultPrivilege" class="li.strolch.privilege.policy.DefaultPrivilege"/>
    <Policy name="RoleAccessPrivilege" class="li.strolch.privilege.policy.RoleAccessPrivilege"/>
    <Policy name="UserAccessPrivilege" class="li.strolch.privilege.policy.UserAccessPrivilege"/>
    <Policy name="UserSessionAccessPrivilege" class="li.strolch.privilege.policy.UsernameFromCertificatePrivilege"/>
  </Policies>
</Privilege>
```

**PrivilegeRoles.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Roles>
  <Role name="User">
    <Privilege name="li.strolch.service.api.Service" policy="DefaultPrivilege">
    </Privilege>

    <Privilege name="li.strolch.search.StrolchSearch" policy="DefaultPrivilege">
        <AllAllowed>true</AllAllowed>
        <Allow>li.strolch.bookshop.search.BookSearch</Allow>
    </Privilege>

    <Privilege name="GetResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="GetOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="GetActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="AddResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="AddOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="AddActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="UpdateResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="UpdateOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="UpdateActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="RemoveResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="RemoveOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="RemoveActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
  </Role>
  <Role name="UserPrivileges">
    <Privilege name="PrivilegeSetUserLocale" policy="UserAccessPrivilege" />
    <Privilege name="PrivilegeSetUserPassword" policy="UserAccessPrivilege" />
  </Role>

  <!--
    Internal
  -->
  <Role name="StrolchAdmin">
    <Privilege name="li.strolch.service.api.Service" policy="DefaultPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>

    <Privilege name="li.strolch.search.StrolchSearch" policy="DefaultPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>

    <Privilege name="GetResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="GetOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="GetActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="AddResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="AddOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="AddActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="UpdateResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="UpdateOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="UpdateActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="RemoveResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="RemoveOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="RemoveActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>

    <Privilege name="PrivilegeAddUser" policy="UserAccessPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="PrivilegeSetUserPassword" policy="UserAccessPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>
  </Role>

  <Role name="agent">
    <Privilege name="li.strolch.privilege.handler.SystemAction" policy="DefaultPrivilege">
      <Allow>li.strolch.runtime.privilege.StrolchSystemAction</Allow>
      <Allow>li.strolch.runtime.privilege.StrolchSystemActionWithResult</Allow>
      <Allow>li.strolch.persistence.postgresql.PostgreSqlSchemaInitializer</Allow>
    </Privilege>

    <Privilege name="li.strolch.service.api.Service" policy="DefaultPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>

    <Privilege name="li.strolch.model.query.StrolchQuery" policy="DefaultPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="li.strolch.search.StrolchSearch" policy="DefaultPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>

    <Privilege name="GetResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="GetOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="GetActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="AddResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="AddOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="AddActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="UpdateResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="UpdateOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="UpdateActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="RemoveResource" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="RemoveOrder" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="RemoveActivity" policy="ModelPrivilege">
        <AllAllowed>true</AllAllowed>
    </Privilege>

    <Privilege name="PrivilegeAction" policy="DefaultPrivilege">
      <Allow>Persist</Allow>
      <Allow>PersistSessions</Allow>
      <Allow>GetCertificates</Allow>
    </Privilege>
    <Privilege name="PrivilegeAddUser" policy="UserAccessPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="PrivilegeModifyUser" policy="UserAccessPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="PrivilegeGetUser" policy="UserAccessPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>
  </Role>

</Roles>
```

**PrivilegeUsers.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Users>
  <User userId="U10" username="jill" password="8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918">
    <Firstname>Jill</Firstname>
    <Lastname>Someone</Lastname>
    <State>ENABLED</State>
    <Locale>en-GB</Locale>
    <Roles>
      <Role>User</Role>
      <Role>UserPrivileges</Role>
    </Roles>
    <Properties>
      <Property name="email" value="eitch+jill@eitchnet.ch" />
    </Properties>
  </User>

  <User userId="U01" username="admin" password="8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918">
    <Firstname>Jill</Firstname>
    <Lastname>Someone</Lastname>
    <State>ENABLED</State>
    <Locale>en-GB</Locale>
    <Roles>
      <Role>StrolchAdmin</Role>
      <Role>UserPrivileges</Role>
    </Roles>
    <Properties>
      <Property name="email" value="eitch+admin@eitchnet.ch" />
    </Properties>
  </User>

  <!--
    Internal
  -->
  <User userId="S01" username="agent">
    <State>SYSTEM</State>
    <Roles>
      <Role>agent</Role>
    </Roles>
  </User>

</Users>
```

**StrolchConfiguration.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<StrolchConfiguration>
  <env id="global">
    <Runtime>
      <applicationName>Bookshop</applicationName>
      <Properties>
        <locale>en</locale>
        <verbose>true</verbose>
      </Properties>
    </Runtime>

    <Component>
      <name>PrivilegeHandler</name>
      <api>li.strolch.runtime.privilege.PrivilegeHandler</api>
      <impl>li.strolch.runtime.privilege.DefaultStrolchPrivilegeHandler</impl>
      <Properties>
        <privilegeConfigFile>PrivilegeConfig.xml</privilegeConfigFile>
      </Properties>
    </Component>

    <Component>
      <name>RealmHandler</name>
      <api>li.strolch.agent.api.RealmHandler</api>
      <impl>li.strolch.agent.impl.DefaultRealmHandler</impl>
      <depends>PrivilegeHandler</depends>
      <Properties>
        <realms>defaultRealm</realms>

        <dataStoreMode>TRANSIENT</dataStoreMode>
        <dataStoreFile>defaultModel.xml</dataStoreFile>
        <enableObserverUpdates>true</enableObserverUpdates>
      </Properties>
    </Component>

    <Component>
      <name>ServiceHandler</name>
      <api>li.strolch.service.api.ServiceHandler</api>
      <impl>li.strolch.service.api.DefaultServiceHandler</impl>
      <depends>RealmHandler</depends>
      <depends>PrivilegeHandler</depends>
      <Properties>
        <verbose>true</verbose>
      </Properties>
    </Component>

    <Component>
      <name>PolicyHandler</name>
      <api>li.strolch.policy.PolicyHandler</api>
      <impl>li.strolch.policy.DefaultPolicyHandler</impl>
      <Properties>
        <readPolicyFile>true</readPolicyFile>
      </Properties>
    </Component>

    <Component>
      <name>ExecutionHandler</name>
      <api>li.strolch.execution.ExecutionHandler</api>
      <impl>li.strolch.execution.EventBasedExecutionHandler</impl>
      <depends>RealmHandler</depends>
      <depends>PrivilegeHandler</depends>
    </Component>

    <Component>
      <name>RestfulHandler</name>
      <api>li.strolch.rest.RestfulStrolchComponent</api>
      <impl>li.strolch.rest.RestfulStrolchComponent</impl>
      <depends>SessionHandler</depends>
      <Properties>
        <secureCookie>false</secureCookie>
        <restLogging>false</restLogging>
        <restLoggingEntity>false</restLoggingEntity>
        <restTracing>ALL</restTracing>
      </Properties>
    </Component>
    <Component>
      <name>SessionHandler</name>
      <api>li.strolch.rest.StrolchSessionHandler</api>
      <impl>li.strolch.rest.DefaultStrolchSessionHandler</impl>
      <depends>PrivilegeHandler</depends>
      <Properties>
        <session.ttl.minutes>30</session.ttl.minutes>
        <session.reload>true</session.reload>
      </Properties>
    </Component>

    <Component>
      <name>MailHandler</name>
      <api>li.strolch.handler.mail.MailHandler</api>
      <impl>li.strolch.handler.mail.SmtpMailHandler</impl>
      <Properties>
        <fromAddr>relayer@eitchnet.ch</fromAddr>
        <fromName>Susi</fromName>
        <overrideRecipients><![CDATA[IPSC Test <eitch@eitchnet.ch>]]></overrideRecipients>
        <recipientWhitelist>eitch@eitchnet.ch</recipientWhitelist>
        <username>test</username>
        <password>test</password>
        <auth>true</auth>
        <startTls>true</startTls>
        <host>smtp.gmail.com</host>
        <port>587</port>
      </Properties>
    </Component>

  </env>

  <env id="dev">
    <!-- overrides go here -->
  </env>

</StrolchConfiguration>
```

**StrolchPolicies.xml**

```xml
<StrolchPolicies>
  <PolicyType Type="ExecutionPolicy" Api="li.strolch.execution.policy.ExecutionPolicy">
    <Policy Key="DurationExecution" Class="li.strolch.execution.policy.DurationExecution" />
    <Policy Key="ReservationExection" Class="li.strolch.execution.policy.ReservationExecution" />
  </PolicyType>
  <PolicyType Type="ConfirmationPolicy" Api="li.strolch.execution.policy.ConfirmationPolicy">
    <Policy Key="DefaultConfirmation" Class="li.strolch.execution.policy.ConfirmationPolicy" />
  </PolicyType>
  <PolicyType Type="ActivityArchivalPolicy" Api="li.strolch.execution.policy.ActivityArchivalPolicy">
    <Policy Key="DefaultActivityArchival" Class="li.strolch.execution.policy.ActivityArchivalPolicy" />
  </PolicyType>
</StrolchPolicies>
```

A few notes on the configuration:

* Note how there are three users. Jill is a user with currently no privileges as
  it's role definition is empty. Admin can do everything, and the agent user is
  a system user which can also do everything.
* There is one realm defined in the `RealmHandler` component which references
  the
  `defaultModel.xml` file in the `data` directory. This file then includes the
  currently still empty `templates.xml` file.
* We have defined a `global` environment, but are using the `dev` environment.
  The dev environment includes the definitions in the global environment.
* In `PrivilegeConfig.xml` we have enabled persistence of sessions, so you will
  be needing the unlimited JCE libraries for your JVM.

  When you restart the server, you don't need to log back in, if your session is
  still alive.
* In `PrivilegeRoles.xml` there seems to be a lot of boilerplate. One thing about
  a highly configurable system is that sometimes the configuration is bigger. In
  this case we have opted to have the configuration shown and not use default
  values which you don't see, so that privilege access is clearly seen.

Your project is now ready to be imported into your favourite IDE. We have used
both IntelliJ and Eclipse so this is up to you.

Now that we have a configuration, it is time to have Strolch started when the
WAR is deployed and started. In your IDE create a new class as follows:

**StartupListener.java**

```java
package li.strolch.bookshop.web;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;
import java.io.InputStream;

import li.strolch.agent.api.StrolchAgent;
import li.strolch.agent.api.StrolchBootstrapper;
import li.strolch.utils.helper.StringHelper;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@WebListener
public class StartupListener implements ServletContextListener {

  private static final Logger logger = LoggerFactory.getLogger(StartupListener.class);
  private static final String APP_NAME = "Bookshop";

  private StrolchAgent agent;

  @Override
  public void contextInitialized(ServletContextEvent sce) {

    logger.info("Starting " + APP_NAME + "...");
    long start = System.currentTimeMillis();
    try {
      String boostrapFileName = "/WEB-INF/" + StrolchBootstrapper.FILE_BOOTSTRAP;
      InputStream bootstrapFile = sce.getServletContext().getResourceAsStream(boostrapFileName);
      StrolchBootstrapper bootstrapper = new StrolchBootstrapper(StartupListener.class);
      this.agent = bootstrapper.setupByBoostrapFile(StartupListener.class, bootstrapFile);
      this.agent.initialize();
      this.agent.start();
    } catch (Throwable e) {
      logger.error("Failed to start " + APP_NAME + " due to: " + e.getMessage(), e);
      throw e;
    }

    long took = System.currentTimeMillis() - start;
    logger.info("Started " + APP_NAME + " in " + (StringHelper.formatMillisecondsDuration(took)));
  }

  @Override
  public void contextDestroyed(ServletContextEvent sce) {
    if (this.agent != null) {
      logger.info("Destroying " + APP_NAME + "...");
      try {
        this.agent.stop();
        this.agent.destroy();
      } catch (Throwable e) {
        logger.error("Failed to stop " + APP_NAME + " due to: " + e.getMessage(), e);
        throw e;
      }
    }
    logger.info("Destroyed " + APP_NAME);
  }
}
```

Now configure your IDE to start the web project, and then once it has started,
you should see the following in the logs:

```text
Bookshop:dev All 8 Strolch Components started. Took 44ms. Strolch is now ready to be used. Have fun =))
```

This log tells us the name of the app as defined in the StrolchConfiguration.xml
file as well as which environment was loaded. Further we can see that 8
components were configured and started.

This concludes the initial setup of a new Strolch project. We can now go ahead
and start building the business logic.



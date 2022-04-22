---
title: 'Development'
weight: 90
---

## Prerequisites
To start developing Strolch you need an installed:
* Java JDK 17
* Apache Maven 3.x

## Building Strolch

{{% notice tip %}} Note: You don't have to build Strolch if you want to use the
version on Maven central, but if you need a snapshot version, or the release you
want isn't on central, then go ahead and build Strolch. {{% /notice %}}

Building Strolch is just a few lines:

```shell
git clone https://github.com/strolch-li/strolch.git
cd strolch
mvn clean install -DskipTests
```

{{% notice warning %}}
Note: To run the tests you will need to configure the PostgreSQL Databases. See
the README in the module.
{{% /notice %}}

After running the Maven build, you will have a full build of all Strolch
projects. Now you can start modifying the projects, and add your own features,
or, far more interesting, start developing your projects using the Strolch
agent.

## Creating a Strolch App

To create your own Strolch App, you can use Maven's archetype generation. There
are two versions, one is a simple Java App which you can use to directly access
the Strolch runtime, and the second is to create a Java Web App, which is the
usual way to run Strolch runtimes.

{{% notice warning %}}
Note: you need to have installed Strolch to your local maven repo, otherwise the
archetype won't be available.
{{% /notice %}}

### Creating a Java Strolch Web App

The following shows the maven command to create the new maven project. Note that you should replace the placeholders in the brackets:

{{% notice tip %}}
The code is also available on [GitHub](https://github.com/strolch-li/strolch/tree/develop/li.strolch.mvn.archetype.webapp).
{{% /notice %}}

```shell
mvn archetype:generate \
  -DarchetypeGroupId=li.strolch \
  -DarchetypeArtifactId=li.strolch.mvn.archetype.webapp \
  -DarchetypeVersion=1.8.0-SNAPSHOT \
  -DgroupId=<my.groupid> \
  -DartifactId=<my-artifactId> \
  -Dversion=<my.version> \
  -Dpackage=<my.package> \
  -DappName="<my app name>"
```

#### Install the web dependencies

The Strolch Web App uses [NodeJS v11.x](https://nodejs.org/download/release/v11.15.0/) to build the web dependencies. Please
download the relevant platform's package, unpack it, and add the `bin` directory
to your path variable.

Once NodeJS is installed, then you can prepare the web dependencies:

```shell
cd src/main/webapp/
npm install bower -g
npm install gulp -g
npm install
bower install
gulp
cd ../../..
```

{{% notice warning %}} Note: Whenever the bower.json is changed then you should
again call npm install inside the webapp folder. {{% /notice %}}

#### Building and running the WAR
Building the WAR uses the `package` maven goal, but to have the optimized WAR
use the `release` profile. 

To run this WAR, we must configure the path of Strolch's runtime in the `StrolchBootstrap.xml` file, by updating the following block, 
setting the absolute path to the `runtime` from the new project:
```xml
<env id="test" default="true">
  <root>/absolute/path/to/&lt;my-artifactId&gt;/runtime</root>
  <environment>test</environment>
</env>
```

Now we can build the project using the configured environment:
```shell
mvn clean package -Prelease -Dstrolch.env=test
```

Now copy the WAR from the `target/` directory to the `webapps/` directory of your Tomcat 9.x installation. Now you can start tomcat using:
```shell
catalina.sh run
```

In the console you'll then see something like this:
```log
INFO  l.s.a.impl.ComponentContainerImpl:283 start - System: OS: Linux 5.16.15 Arch: amd64 on Java Azul Systems, Inc. 17 CPU Cores: 32
INFO  l.s.a.impl.ComponentContainerImpl:284 start - Memory: Memory available 16.8 GB / Used: 604.0 MB / Free: 540.6 MB
INFO  l.s.a.impl.ComponentContainerImpl:285 start - Using locale en with timezone Europe/Zurich
INFO  l.s.a.impl.ComponentContainerImpl:288 start - file.encoding: UTF-8 / sun.jnu.encoding UTF-8
INFO  l.s.a.impl.ComponentContainerImpl:307 start - eSyTest:test All 12 Strolch Components started. Took 43ms. Strolch is now ready to be used. Have fun =))
INFO  c.atexxi.esytest.web.StartupListener:43 contextInitialized - Started eSyTest in 244ms
```

And now you can open a browser and login: `http://localhost:8080/<my-artifactId>`

The default username and password are `admin` / `admin`. After logging you, you should see the following view:

![Strolch Demo App](/assets/images/demo-app.png)

Happy coding =))

### Creating a simple Java Strolch App

The following shows the maven command to create the new maven project. Note that
you should replace the placeholders in the brackets:

{{% notice tip %}}
The code is also available on [GitHub](https://github.com/strolch-li/strolch/tree/develop/li.strolch.mvn.archetype.main).
{{% /notice %}}

```shell
mvn archetype:generate \
  -DarchetypeGroupId=li.strolch \
  -DarchetypeArtifactId=li.strolch.mvn.archetype.main \
  -DarchetypeVersion=1.8.0-SNAPSHOT \
  -DgroupId=<my.groupid> \
  -DartifactId=<my-artifactId> \
  -Dversion=<my.version> \
  -DappName="<my app name>"
```

You change into the directory of the new project and then build the project by calling:

```shell
cd <my-artifactId>
mvn clean package
```

Start the program using:

```shell
mvn exec:java
```

Happy coding =))

## Converting an existing application

You can convert an existing application to a Strolch agent, but this might be a
bit daunting in the beginning. If you are planning on doing this, first create a
test application using the maven archetypes, so that you can get a feel for the configuration.

Once that works, use the archetypes configuration to reconfigure your project to start as a Strolch agent.

{{% notice warning %}}
Note: Beware to select the archetype pertaining to your use case:
* For a web app use the [li.strolch.mvn.archetype.webapp](/development/#creating-a-java-strolch-web-app)
* For a application with a main method use [li.strolch.mvn.archetype.main](/development/#creating-a-simple-java-strolch-app)
  {{% /notice %}}

## Tools used

The following tools are used to develop Strolch and Strolch-based projects: 

* [IntelliJ](https://www.jetbrains.com/idea/download/#section=linux)
* [Apache Maven](https://maven.apache.org/)
* [Git SCM](http://git-scm.com/)


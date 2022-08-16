---
title: 'Web App'
weight: 41
---

## Prerequisites
To start developing web-based Strolch apps you need the following:
* Apache Tomcat 9.x (10.x isn't supported yet). Just unpack it somewhere, to be used later, when running the application.
* NodeJS v11.x (other versions don't work with [Bower](https://bower.io), the installation is described below.

{{% notice tip %}} Note: Strolch's Web UI is still using 
[Polymer 1.x](https://polymer-library.polymer-project.org/1.0/docs/devguide/feature-overview). This isn't a big 
concern, as thanks to the polyfills, it works on all browsers, including Internet Explorer 11. {{% /notice %}}

## Install the web dependencies

The Strolch Web App uses [NodeJS v11.x](https://nodejs.org/download/release/v11.15.0/) to build the web dependencies. Please
download the relevant platform's package, unpack it, and add the `bin` directory
to your path variable and add a `NODE_HOME` variable pointing to the root directory of the NodeJS folder.

Test that NodeJS is working properly:
```shell
$ npm --version
6.8.0
```

Once NodeJS is installed, then you can prepare the web dependencies:

```shell
npm install -g bower
npm install -g gulp
```

And then check that both bower and gulp work as expected:
```shell
$ bower --version
1.8.14

$ gulp --version
[17:22:56] CLI version 2.0.1
```

## Creating a Strolch Web App

The following shows the maven command to create the new maven project using Strolch's webapp maven archetype. Note that you should replace the placeholders in the brackets:

{{% notice warning %}}
Note: you need to have the Strolch Maven archetypes installed to your local maven repo, otherwise the
following command will fail.
{{% /notice %}}

```shell
mvn archetype:generate \
  -DarchetypeGroupId=li.strolch \
  -DarchetypeArtifactId=li.strolch.mvn.archetype.webapp \
  -DarchetypeVersion=0.1.0-SNAPSHOT \
  -DgroupId=<my.groupid> \
  -DartifactId=<my-artifactId> \
  -Dversion=<my.version> \
  -Dpackage=<my.package> \
  -DappName="<my app name>"
```

## Installing the web dependencies
In the newly generated artifact, change to the `webapp` folder, and then run `npm install`:

```shell
cd <my-artifactId>/src/main/webapp
npm install
```

This will first install the NodeJS modules, and then call bower to install the bower packages.

{{% notice warning %}} Note: Whenever the bower.json is changed, e.g. updating a version or adding a package, then you should
again call `npm install` inside the webapp folder. {{% /notice %}}

### Building the WAR


### Running the WAR
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
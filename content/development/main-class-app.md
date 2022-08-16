---
title: 'Main Class App'
weight: 50
---

## Creating a Strolch App

The following shows the maven command to create the new maven project using Strolch's main maven archetype. Note that
you should replace the placeholders in the brackets:

{{% notice warning %}}
Note: you need to have the Strolch Maven archetypes installed to your local maven repo, otherwise the
following command will fail.
{{% /notice %}}

```shell
mvn archetype:generate \
  -DarchetypeGroupId=li.strolch \
  -DarchetypeArtifactId=li.strolch.mvn.archetype.main \
  -DarchetypeVersion=0.1.0-SNAPSHOT \
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

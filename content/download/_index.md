---
title: 'Download'
weight: 80
---

## Download

Strolch is
on [Maven central](https://mvnrepository.com/artifact/li.strolch/li.strolch.agent)
, but if the latest version is not there, then build it locally. A guide can be
found on the [development](/development) page.

Strolch is also built on [Jenkins](https://ci.4trees.ch/), so you can see if the
latest version passes all tests.

## Include as Maven dependency

The easiest way to include strolch in your project is to use the following maven dependency:

```xml

<project>
    
    <properties>
        <strolch.version>1.6.100</strolch.version>
    </properties>
    
    <dependencies>
        <dependency>
            <groupId>li.strolch</groupId>
            <artifactId>li.strolch.bom</artifactId>
            <type>pom</type>
            <version>${strolch.version}</version>
            <scope>import</scope>
        </dependency>
    </dependencies>
</project>

```

The bom will include all Strolch modules, you can always exclude a dependency,
or only include the dependencies you really want, e.g. model, etc.

After including the dependency, checkout [development](/development) on how to turn your project into a Strolch agent.

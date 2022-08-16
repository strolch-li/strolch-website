---
title: 'Building Strolch'
weight: 20
---

## Building Strolch
{{% notice tip %}} Note: You don't have to build Strolch if you want to use the
version on Maven central. If you need a snapshot version, the release you
want isn't on Maven central or you want to use the Strolch Maven archetypes, then go ahead and build Strolch. {{% /notice %}}

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
modules. Now you can start modifying the modules, and add your own features,
or, far more interesting, start developing your projects using the Strolch
agent.

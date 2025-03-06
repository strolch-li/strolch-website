---
title: 'Prerequisites'
weight: 10
---

## Prerequisites
To start developing Strolch you need an installed:
* Java JDK 23
* Apache Maven 3.x

You can install these using the awesome [SDKMAN!](https://sdkman.io/):
```shell
$ curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk version

sdk install java
sdk install maven
sdk install mvnd
```

Test your Java installation:
```shell
$ java -version
openjdk version "23.0.1" 2024-10-15
OpenJDK Runtime Environment Zulu23.30+13-CA (build 23.0.1+11)
OpenJDK 64-Bit Server VM Zulu23.30+13-CA (build 23.0.1+11, mixed mode, sharing)
```

Test your Maven installation:
```shell
$ mvn --version
Apache Maven 3.9.9 (8e8579a9e76f7d015ee5ec7bfcdc97d260186937)
Maven home: /home/user/.sdkman/candidates/maven/current
Java version: 23.0.1, vendor: Azul Systems, Inc., runtime: /home/user/.sdkman/candidates/java/23.0.1-zulu
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "6.14.0-061400rc3-generic", arch: "amd64", family: "unix"
```

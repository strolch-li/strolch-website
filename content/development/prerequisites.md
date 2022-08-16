---
title: 'Prerequisites'
weight: 10
---

## Prerequisites
To start developing Strolch you need an installed:
* Java JDK 17
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
openjdk version "17.0.4" 2022-07-19
OpenJDK Runtime Environment Temurin-17.0.4+8 (build 17.0.4+8)
OpenJDK 64-Bit Server VM Temurin-17.0.4+8 (build 17.0.4+8, mixed mode, sharing)
```

Test your Maven installation:
```shell
$ mvn --version
Apache Maven 3.8.6 (84538c9988a25aec085021c365c560670ad80f63)
Maven home: /home/user/.sdkman/candidates/maven/current
Java version: 17.0.4, vendor: Eclipse Adoptium, runtime: /home/user/.sdkman/candidates/java/17.0.4-tem
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "5.18.11-051811-generic", arch: "amd64", family: "unix"
```

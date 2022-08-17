---
title: 'Converting Existing App'
weight: 90
---

## Converting an existing application

You can convert an existing application to a Strolch agent, but this might be a
bit daunting in the beginning. If you are planning on doing this, first create a
test application using the maven archetypes, so that you can get a feel for the configuration.

Once that works, use the archetypes configuration to reconfigure your project to start as a Strolch agent.

{{% notice warning %}}
Note: Beware to select the archetype pertaining to your use case:

* For a web app use the [li.strolch.mvn.archetype.webapp](/development/web-app)
* For an application with a main method use [li.strolch.mvn.archetype.main](/development/main-class-app)
  {{% /notice %}}

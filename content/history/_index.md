---
title: 'History'
weight: 1000
hidden: true
---

## Overview
Strolch is an open source component based software agent written in Java and can be compared, in a light sense, with the Java EE stack: Strolch takes care of persistence, implements Services for use cases, Commands as re-usable algorithms and has a parameterized data model.

Strolch has an intrinsic understanding for mandates, which are called realms so that a single agent can be used to implement applications with multiple users/customers for instance in SaaS environments.

The parameterized data model consists of three top level objects, Resources, Orders and Activities. These objects can have any number of ParameterBags which in turn can have any number of Parameters on them. This allows for a very dynamic modelling of data structures including modification at run time. Multiple ready to use Parameter types are already implemented which handle the primitive types in Java including ListParameters for collections of these primitive types.

One of the main features of the Strolch agent, is that persistence is handled transparently and the user must not be worried about databases and the likes. Currently there are two implementations for persisting the Strolch model, a PostgreSQL and an XML file persistence. Currently both persistence layers persist the data by converting to XML and storing it into the database. The XML file persistence stores each object in its own file.

The agent itself has a small memory footprint and requires very few components to start. For the agent to be useful it needs additional functionality which is implemented in StrolchComponents. Each component is registered via its Java interface on the agent and is bound to the life cycle of the agent. When the agent is started, these components can be retrieved and used to perform any number of functionalities. This is the preferred way to extend the Strolch agent. There are a number of components already implemented, e.g. the ServiceHandler which executes Services in a controlled fashion and can validate authorized access to these services.

No software product is complete without a system for authentication and authorization. Strolch implements this by using the Privilege framework which has been written by Robert von Burg. The standard ServiceHandler detects the existence of the PrivilegeHandler and then validates that the user has authorization to perform the service. This framework is implemented as its own Strolch component, thus can be retrieved at any time during execution to perform fine grained and special authorization validation.

## Motivation
A question often asked is why create Strolch. What are its benefits in contrast to using Java SE with an OR-Mapper like Hibernate, or using Java EE on JBoss or Glassfish? Especially since many of the features existing in those stacks needed to be re-created in Strolch.

The first answer to this question is that those systems are often overly complicated and bloated. Java SE with Hibernate certainly is a viable option when it comes to being light-weightier but Hibernate, even though it is supposed to, often fails to truly help remove the need to really understand an RDBMS. Often enough Hibernate will just get in the way of the most important part of development: writing the business code. Being an OR-Mapper which is supposed to implement all the nitty-gritty details of an RDBMS system, Hibernate, and JPA for that matter, still often has the developer go back to understanding these details.

Strolch tries a different approach to persistence. Instead of writing pojos/entities, Strolch's model has the concept that each element's attributes are part of a composition pattern: each attribute is its own object and thus can be dynamically changed at runtime, but also makes persistence of such an element generic. Instead of having fixed attributes for a concrete class, these parameters are stored in a map and are accessed through the parameter's ID.

Assigning an ID to an attribute for accessing of course brings its own downsides, i.e. the parameter might simply not be there, when being accessed. This is certainly an issue that the developer must handle, when implementing a project using Strolch, but allows the developer to not need to worry about persistence, as this is generically handled.

Since the persistence is generically handled, and Strolch stays lightweight on its requirements at runtime, the developer can quickly get down to what is important for business value: Writing the business logic and the presentation layer. Here too Strolch tries to help the developer by bringing in concepts which are easy to follow: Use cases are implemented as Services, and re-usable business logic is put into Commands.

There will be reasons against using Strolch, as there will be against using the Java EE stack, or an OR-Mapper or even the Java ecosystem for that fact. Important is to note, that the concepts behind Strolch are nothing new, but have been implemented in at least two previous proprietary products. Since those products are not accessible to the public, it was decided that a re-implementation might be of use to the programming community at large.

Currently, there is at least one company using Strolch in a commercial project which helps drive Strolch's development and further motivates its existence.

Strolch is an open source project and licensed under the Apache License 2.0.

##Technology
Strolch is written in Java and is programmed against the JDK 8. Strolch runs on any JRE 8 compliant environment. Strolch is tested on the Oracle JRE 8.
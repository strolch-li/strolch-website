---
title: Strolch Overview
---

## Strolch Overview

Strolch is framework for developing Software. It's main features are:

* Complete persisted data model:
  * Parameters and values by time 
  *  Resources, Orders with arbitrary parameter grouping 
  *  Activity/Action hierarchy with arbitrary depth
  *  Policies for delegation
  *  JSON as well as XML transformation
  *  Locator API
* Transactions with pessimistic locking and optional read-locking
* Search API
* Component based
* Deeply integrated privilege handling
* Fully in-memory
* Persisted auditing, versioning, operations log
* DAOs for file system or PostgreSQL, easily extended
* Execution framework
* Service / Command oriented
* Reporting API configured by Resource objects
* REST API for data access
* WebComponents UI for
  * Inspector
  * Users
  * Roles
  * Operations Log
  * Login Screen
  * Jobs
* runs on plain old Java SE

## Strolch Intro
It is a different framework to Spring and other similar type of Java 
frameworks, as the model is defined as an abstract model, where you 
always have the same three types of objects: Resources, Orders and 
Activities. The fields are mapped as Parameter objects, of which the 
important primitives are available.

The nice part about this framework is, that you can be up and ready in 
a matter of minutes, and start building your project immediately in 
that you open your favourite XML editor and start modelling your data.

Once your data is defined, you write your business logic in the form 
of Services, Commands and Searches. There are many predefined services 
and commands to manipulate the object model, so that you write your own 
services when you need to enforce special business rules.

Through the use of Policy objects, you decouple algorithms from your 
object model, so that at runtime you can change the behaviour, or 
easily implement different behaviour depending on your use-case. For 
instance you might have a simple billing service which performs a few 
preparatory steps, and then calls the configured billing policy to 
execute the billing depending on the customer, the warehouse, etc.

And of course persistence is as simple as configuring the persistence 
handler, pointing to your RDBMS and then setting the mode to CACHED. 
For you as a developer there is no more thinking in terms of SQL etc., 
as this is completely hidden from the developer. There is even a simple 
file persistence layer if you are running IoT devices.

The runtime can be just about anything. Usually it is run inside an 
Apache Tomcat instance as a webapp, as a WEB UI has been required for 
all current Strolch projects. You could just as well use a main class. 
Accessing the Strolch Agent remotely is usually done through REST.

Strolch is being actively developed, and customers constantly give us 
reasons to improve and extend the framework. There is a Polymer 
Inspector component which makes it easy to see and manipulate the 
actual data. The new Search API makes it really easy to query your data.

Yes, Strolch is different, but the concept has come out of the planning 
and execution segment, and has been refined over the years until it has 
become what it is today.

## API
Check out the API page to see how to use Strolch.

[**More to motivation etc**](/history/).
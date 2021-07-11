---
title: 'Queries' 
weight: 89
---

## Queries
{{% notice warning %}}
The Query API is deprecated and the search API should be used instead.
{{% /notice %}}

As is custom for every framework, querying the model must be possible. Strolch
queries are implemented using the `StrolchQuery` interface and one of its concrete
implementations: `ResourceQuery`, `OrderQuery`, `ActivityQuery`.

A Strolch element always has two identifiers: `Type` and `Id`. The type is important
as it classifies an element. So if a car and a house would be modelled in
Strolch, then those would both be a `Resource`, but one of type `Car` and the other
of type `House`. Both would have different parameters.

Thus one of the inputs for every query is it's type, which is defined as the
navigation. It is said that we navigate to the Cars, or Houses. Thus when
instantiating a ResourceQuery, pass the navigation to the type of Resource as
well. Same applies for Orders and Activities.

Further input for a StrolchQuery are the selections. These selections get
translated into RDBMS `WHERE` clauses. Selections support boolean operations thus
allowing for complex querying.

StrolchQueries also support Ordering and object transformation. Following
classes provide the most used scenarios:

* OrderById
* OrderByName
* OrderByParameter
* *ToDomVisitor
* *ToSaxVisitor
* *ToJsonVisitor
* *ToFlatJsonVisitor

Example: Query all resources of type Car:
```java
try (StrolchTransaction tx = openTx()) {
  ResourceQuery<Resource> query = ResourceQuery.query("Car");
  query.withAny();
  List<Resource> cars = tx.doQuery(query);
}
```

Example: Query all resources of type Car, order by Name and transform to JSON:

```java
try (StrolchTransaction tx = openTx()) {
  ResourceQuery<JsonObject> query = ResourceQuery.query("Car", new ResourceToJsonVisitor(),
      new OrderByName());
  query.withAny();
  List<JsonObject> cars = tx.doQuery(query);
}
```

the previous example can also be written as follows:
```java
try (StrolchTransaction tx = openTx()) {
  ResourceQuery<JsonObject> query = new ResourceQuery<>();
  query.setNavigation(new StrolchTypeNavigation("Car"));
  query.setResourceVisitor(new ResourceToJsonVisitor());
  query.withAny();
  List<JsonObject> cars = tx.doQuery(query);
}
```

Example: Query all resources of type Car with color blue:
```java
try (StrolchTransaction tx = openTx()) {
    ResourceQuery<Resource> query = ResourceQuery.query("Car");
    query.with(ParameterSelection.stringSelection("parameters", "color", "blue", StringMatchMode.es()));
    List<Resource> cars = tx.doQuery(query);
}
```

Example: Query all resources of type Car which are not blue:
```java
try (StrolchTransaction tx = openTx()) {
  ResourceQuery<Resource> query = ResourceQuery.query("Car");
  query.not(ParameterSelection.stringSelection("parameters", "color", "blue", StringMatchMode.es()));
  List<Resource> cars = tx.doQuery(query);
}
```

Example: Query all resources of type Car with color blue or yellow:
```java
try (StrolchTransaction tx = openTx()) {
  ResourceQuery<Resource> query = ResourceQuery.query("Car");
  query.or().with(
      ParameterSelection.stringSelection("parameters", "color", "blue", StringMatchMode.es()),
      ParameterSelection.stringSelection("parameters", "color", "yellow", StringMatchMode.es()));
  List<Resource> cars = tx.doQuery(query);
}
```

Example: Query all resources of type Car with color blue or yellow owned by Jill:
```java
try (StrolchTransaction tx = openTx()) {
  ResourceQuery<Resource> query = ResourceQuery.query("Car");

  StringParameterSelection owner = ParameterSelection.stringSelection("parameters", "owner", "Jill", StringMatchMode.es());
  OrSelection colors = new OrSelection().with(
      ParameterSelection.stringSelection("parameters", "color", "blue", StringMatchMode.es()),
      ParameterSelection.stringSelection("parameters", "color", "yellow", StringMatchMode.es()));

  query.and().with(owner, colors);

  List<Resource> cars = tx.doQuery(query);
}
```


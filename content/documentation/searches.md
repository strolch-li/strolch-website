---
title: 'Searches' 
weight: 80
---

## Searches

As is custom for every framework, querying, or searching, the model must be
possible. Strolch searches are implemented using the `StrolchSearch` class and
one of its concrete implementations: `ResourceSearch`, `OrderSearch`,
`ActivitySearch`.

A Strolch element always has two identifiers: `Type` and `Id`. The type is
important as it classifies an element. So if a car and a house would be modelled
in Strolch, then those would both be a `Resource`, but one of type `Car`
and the other of type `House`. Both would have different parameters. Thus when
searching for objects, the first thing to do is define the type of object being
searched.

The Strolch search API is very expressive and offers multiple ways to perform
the same search. The search API consists of three components: The search
classes, the search expressions and the search predicates. The concept was taken
from the [Apache Camel](https://camel.apache.org/) project.

There are four main search classes:

* RootElementSearch - search for any of Resource, Order or Activity elements
* ResourceSearch - search for Resources
* OrderSearch - search for Orders
* ActivitySearch - search for Activities

No search is useful without a `where` clause, which are called search
expressions. When writing a search, there are multiple ways to add such where
clauses. Either

* override the `define()`-method in your sub class and add the where clauses by
  calling the `where()` method, or
* define special methods on the class e.g. `byColor()` which also calls the
  `where()`-method to add a search expression, or
* directly call the `where()`-method after instantiating a search.

When extending the class, then the search expressions are available as methods
on the super class, otherwise you can statically import them from
[ExpressionsSupport](https://github.com/4treesCH/strolch/blob/develop/li.strolch.agent/src/main/java/li/strolch/search/ExpressionsSupport.java)
.

And of course a where clause needs operators, which are called search
predicates. Just as search expressions are available in sub classes, so are
search predicates and can also be statically imported through
[PredicatesSupport](https://github.com/4treesCH/strolch/blob/develop/li.strolch.agent/src/main/java/li/strolch/search/PredicatesSupport.java)
.

Examples of search expressions with search predicates follow:

```java
ResourceSearch search=new ResourceSearch();

// predicate either as parameter, or chained
search.where(id().isEqualTo("myId"));
search.where(id(isEqualTo("myId")));

// negating
search.where(id(isEqualTo("myId")).not());

search.where(param("bagId","paramId").isIn(Arrays.asList("red","blue","green")));

search.where(paramNull("bagId","paramId")));

// boolean operations
search.where(id(isEqualTo("myId")) //
		.or(name(isEqualTo("myName"))));
```

Note how the predicates can be chained to the search expression, or passed as a
parameter to the expression.

In addition to using predefined search search expressions, one can also just
pass a lambda expression which performs a custom filter:

```java
personSearch.where(person -> person.getName().length() == 3);
```

See
the [StrolchSearchTest](https://github.com/4treesCH/strolch/blob/develop/li.strolch.agent/src/test/java/li/strolch/search/StrolchSearchTest.java)
for many ways in which you can implement tests.

{{% notice tip %}} Note that strolch searches requires privileges, thus when you
use a strolch search, add it to the role of the user in `PrivilegeRoles.xml`:
{{% /notice %}}

```xml

<Privilege name="li.strolch.search.StrolchSearch" policy="DefaultPrivilege">
    <Allow>internal
    </Allow> <!-- internal used for when the search is done in an internal service -->
    <Allow>li.strolch.bookshop.search.BookSearch</Allow>
</Privilege>
```

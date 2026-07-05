---
title: 'Searches' 
weight: 80
---

## Searches

As is custom for every framework, querying, or searching, the model must be possible. Strolch searches are implemented using the `StrolchSearch` class and one of its concrete implementations: `ResourceSearch`, `OrderSearch`, `ActivitySearch`.

A Strolch element always has two identifiers: `Type` and `Id`. The type is important as it classifies an element. So if a car and a house would be modelled in Strolch, then those would both be a `Resource`, but one of type `Car` and the other of type `House`. Both would have different parameters. Thus when searching for objects, the first thing to do is define the type of object being searched.

The Strolch search API is very expressive and offers multiple ways to perform the same search. The search API consists of three components: The search classes, the search expressions and the search predicates. The concept was taken from the [Apache Camel](https://camel.apache.org/) project.

### Search Classes

*   **`ResourceSearch`**: For querying Resources.
*   **`OrderSearch`**: For querying Orders.
*   **`ActivitySearch`**: For querying Activities.

### Basic Usage

A search is typically executed within a transaction.

```java
List<Resource> results = new ResourceSearch()
    .types("Product")
    .where(id(isEqualTo("p01")))
    .search(tx)
    .toList();
```

### Filtering

Filters are added using the `where` method and `SearchExpression`s. Strolch provides many built-in predicates.

*   **ID and Name**: `id(isEqualTo("..."))`, `name(contains("..."))`
*   **Parameters**: `param("bagId", "paramId", isEqualTo("value"))`
*   **Date Ranges**: `date(isEqualTo(...))`, `date(isBefore(...))`, `date(isAfter(...))`

### Predicates

Strolch supports various predicates for comparison:
*   `isEqualTo(value)`
*   `isNotEqualTo(value)`
*   `contains(value)`
*   `startsWith(value)`
*   `endsWith(value)`
*   `isIn(collection)`
*   `isEmpty()`

### Complex Expressions

Expressions can be combined using logical operators.

```java
new ResourceSearch()
    .types("Person")
    .where(param("parameters", "firstName", isEqualTo("John"))
        .and(param("parameters", "lastName", isEqualTo("Doe"))))
    .search(tx)
    .toList();
```

### Navigation (Relations)

You can navigate through relations between elements. Strolch provides specific expressions for this:

*   **`relationName`**: Filter by the name of a related element.
*   **`relationParam`**: Filter by a parameter of a related element.
*   **`relationNull`**: Filter for elements where a relation is missing.

Note that `relationName` and `relationParam` require the current transaction `tx` to look up the related element.

```java
new ResourceSearch()
    .types("Slot")
    .where(relationName(tx, "location", isEqualTo("Warehouse-A")))
    .search(tx)
    .toList();
```

### Working with Search Results

The `search(tx)` method returns a `SearchResult`, which provides a fluent API for processing the results.

#### Terminal Operations

Terminal operations execute the search and return a result.

*   **`toList()` / `toSet()`**: Collect results into a `List` or `Set`.
*   **`toMap()`**: Collect results into a `Map`.
*   **`toJsonArray(jsonMapper)`**: Convert results to a `JsonArray`.
*   **`toPaging(offset, limit)`**: Return a `Paging` object for paginated results.
*   **`toSingleton()`**: Expect exactly one result, throws an exception otherwise.
*   **`toSingletonO()`**: Returns an `Optional` containing the single result, or empty if none.
*   **`isEmpty()` / `isNotEmpty()`**: Check if any results match.
*   **`forEach(consumer)`**: Perform an action for each result.

#### Transformations and Filtering

*   **`map(mapper)`**: Transform the elements in the result stream.
*   **`filter(predicate)`**: Appends a filter to the result stream.
*   **`asStream()`**: Access the underlying Java `Stream<T>` for custom processing.

#### Ordering

`RootElementSearchResult` provides several convenience methods for sorting:

*   **`orderById(reversed)`**: Sort by element ID.
*   **`orderByName(reversed)`**: Sort by element name.
*   **`orderByParam(bagId, paramId, reversed)`**: Sort by a specific parameter value.
*   **`orderBy(comparator)`**: Sort using a custom `Comparator`.

### Privileges

{{% notice tip %}} Note that strolch searches requires privileges, thus when you use a strolch search, add it to the role of the user in `PrivilegeRoles.xml`: {{% /notice %}}

```xml
<Privilege name="li.strolch.search.StrolchSearch" policy="DefaultPrivilege">
    <Allow>internal</Allow> <!-- internal used for when the search is done in an internal service -->
    <Allow>li.strolch.bookshop.search.BookSearch</Allow>
</Privilege>
```

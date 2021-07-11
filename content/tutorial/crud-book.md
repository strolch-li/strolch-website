---
title: 'CRUD Book'
weight: 30
---

## Preparation

Since Books are central to the bookshop, we'll first create the CRUD REST API
for them. The API will be as follows:

```text
GET ../rest/books?query=,offset=,limit=
GET ../rest/books/{id}
POST ../rest/books
PUT ../rest/books/{id}
DELETE ../rest/books/{id}
```

Thus corresponding with querying, getting, creating, updating and removing of
books. So let's go ahead and add these REST APIs to our project.

Our project is using JAX-RS 2.0 as the API and Jersey 2.x as the implementation,
thus first we need to configure JAX-RS. Thus create the following class:

```java
@ApplicationPath("rest")
public class RestfulApplication extends ResourceConfig {

  public RestfulApplication() {

    // add strolch resources
    register(AuthenticationService.class);
    register(ModelQuery.class);
	register(Inspector.class);

    // add project resources by package name
    packages(BooksResource.class.getPackage().getName());

    // filters
    register(AuthenticationRequestFilter.class, Priorities.AUTHENTICATION);
    register(AccessControlResponseFilter.class);
    register(AuthenticationResponseFilter.class);
    register(HttpCacheResponseFilter.class);

    // log exceptions and return them as plain text to the caller
    register(StrolchRestfulExceptionMapper.class);

    // the JSON generated is in UTF-8
    register(CharsetResponseFilter.class);

    RestfulStrolchComponent restfulComponent = RestfulStrolchComponent.getInstance();
    if (restfulComponent.isRestLogging()) {
      register(new LoggingFeature(java.util.logging.Logger.getLogger(LoggingFeature.DEFAULT_LOGGER_NAME),
          Level.SEVERE, LoggingFeature.Verbosity.PAYLOAD_ANY, Integer.MAX_VALUE));

      property(ServerProperties.TRACING, "ALL");
      property(ServerProperties.TRACING_THRESHOLD, "TRACE");
    }
  }
}
```

As we add new resources they will be automatically since we register the entire package.

Now add the books resource class:

```java
@Path("books")
public class BooksResource {

}
```

## Search

The first service we'll add is to query, or search for the existing books. The
API defines three parameters, with which the result can be controlled. The
method can be defined as follows:

```java

@Path("books")
public class BooksResource {

  @GET
  @Produces(MediaType.APPLICATION_JSON)
  public Response query(@Context HttpServletRequest request,
          @QueryParam("query") String queryS,
          @QueryParam("offset") String offsetS,
          @QueryParam("limit") String limitS) {

    // TODO
  }
}
```

To fill this method we need a few things. First let's define a constants class where we keep String constants which we used in the model file:

```java
public class BookShopConstants {

  public static final String TYPE_BOOK = "Book";

}
```

As this tutorial progresses, more and more constants will be added here. This
class helps with two issues: Through the constants we can easily reason over
where certain fields, and types are used and of course String literals in code
are a rather bad thing.

In Strolch there are multiple way to access objects. The old way was using
Queries, the new search API is much more fluent and easier to read and write.
The search API, as well as the deprecated query API allows us to implement
privilege validation and thus one should create corresponding classes for each
type of search. Book entities are Resources, thus we will be creating a
`ResourceSearch`. The search is for Resources of type Book thus the resulting
search looks as follows:

```java
public class BooksSearch<U> extends ResourceSearch<U> {
  public BookSearch() {
    types(TYPE_BOOK);
  }

  public BookSearch stringQuery(String value) {
    if (isEmpty(value))
      return this;

    // split by spaces
    value = value.trim();
    String[] values = value.split(" ");

    // add where clauses for id, name and description
    where(id().containsIgnoreCase(values) //
            .or(name().containsIgnoreCase(values)) //
            .or(param(BAG_PARAMETERS, PARAM_DESCRIPTION).containsIgnoreCase(values)));

    return this;
  }
}
```

Note how we added a special `stringQuery(String)`-method. This method defines
where a search string entered by the user will be used to match a book. In this
case for `id`, `name` and the `description` parameter.

So that our users can call this query, we must give them this as a privilege.
This is done by adding the full class name to the `PrivilegeRoles.xml` file as
follows:

```xml
...
  <Role name="User">
    <Privilege name="li.strolch.search.StrolchSearch" policy="DefaultPrivilege">
      <Allow>internal</Allow>
      <Allow>li.strolch.bookshop.search.BookSearch</Allow>
    </Privilege>
  </Role>
...
```

{{% notice tip %}} Note: The `internal` allow value is a special privilege which
is used internally when a service or something performs internal queries. This
means that a service can perform a query for object to which the user might not
have access, but without which the service could not be completed. We will use
this in a later stage. {{% /notice %}}

Now we have all parts we need to implement the query method. The method will
include opening a transaction, instantiating the search, executing the search,
and returning the result:

```java

@Path("books")
public class BooksResource {

  @GET
  @Produces(MediaType.APPLICATION_JSON)
  public Response query(@Context HttpServletRequest request,
          @QueryParam("query") String queryS,
          @QueryParam("offset") String offsetS,
          @QueryParam("limit") String limitS) {

    // this is an authenticated method call, thus we can get the certificate from the request:
    Certificate cert = (Certificate) request
            .getAttribute(StrolchRestfulConstants.STROLCH_CERTIFICATE);

    int offset = StringHelper.isNotEmpty(offsetS) ? Integer.parseInt(offsetS) : 0;
    int limit = StringHelper.isNotEmpty(limitS) ? Integer.parseInt(limitS) : 0;

    // open the TX with the certificate, using this class as context
    Paging<Resource> paging;
    try (StrolchTransaction tx = RestfulStrolchComponent.getInstance()
            .openTx(cert, getClass())) {

      // perform a book search
      paging = new BookSearch() //
              .stringQuery(queryS) //
              .search(tx) //
              .orderByName(false) //
              .toPaging(offset, limit);
    }

    ResourceVisitor<JsonObject> visitor = new StrolchRootElementToJsonVisitor()
            .flat().asResourceVisitor();
    return ResponseUtil.toResponse(paging, e -> e.accept(visitor));
  }
}
```

{{% notice tip %}} Note: We automatically transform the Resource objects to JSON
using the `StrolchElementToJsonVisitor`. By calling the method `.flat()`-method we have a
more compact JSON format. Paging is handled by a util class.
{{% /notice %}}

The helper class `ResponseUtil` takes care of creating the `JsonObject` and the
proper page. As a rule we use the format where we return two fields: `msg` is a
dash if all is ok, otherwise an error message will be present. Data is always in
the `data` field. This is just a personal taste, and can be changed to one's own
taste.

## Get
We have all we need now to implement the GET method: 

```java
@GET
@Path("{id}")
@Produces(MediaType.APPLICATION_JSON)
public Response get(@Context HttpServletRequest request, @PathParam("id") String id) {

  // this is an authenticated method call, thus we can get the certificate from the request:
  Certificate cert = (Certificate) request.getAttribute(StrolchRestfulConstants.STROLCH_CERTIFICATE);

  // open the TX with the certificate, using this class as context
  try (StrolchTransaction tx = RestfulStrolchComponent.getInstance().openTx(cert, getClass())) {

    // get the book
    Resource book = tx.getResourceBy(BookShopConstants.TYPE_BOOK, id);
    if (book == null)
      return ResponseUtil.toResponse(Status.NOT_FOUND, "Book " + id + " does not exist!");

    // transform to JSON
    JsonObject bookJ = book.accept(new StrolchRootElementToJsonVisitor().flat());

    // return
    return ResponseUtil.toResponse(StrolchRestfulConstants.DATA, bookJ);
  }
}
```

{{% notice tip %}} Note how we simply retrieve the book as a Resource from the
TX. This is a good moment to familiarize yourself with the API of the
`StrolchTransaction`. There are methods to retrieve elements, and also perform
searches. We will use more of these methods later. {{% /notice %}}

Further it can be noted that a simple retrieval isn't validated against the
user's privileges, the user is authenticated, which is enough for the moment.

## Create

To create a new book we need to implement a `Service`. This service will be called
`CreateBookService`. A Service always has a `ServiceArgument` and a `ServiceResult`.
Our service will use the `JsonServiceArgument` and the `JsonServiceResult`. The
implementation of the POST method is as follows: 

```java
@POST
@Produces(MediaType.APPLICATION_JSON)
public Response create(@Context HttpServletRequest request, String data) {

  // this is an authenticated method call, thus we can get the certificate from the request:
  Certificate cert = (Certificate) request.getAttribute(StrolchRestfulConstants.STROLCH_CERTIFICATE);

  // parse data to JSON
  JsonObject jsonData = JsonParser.parseString(data).getAsJsonObject();

  // instantiate the service with the argument
  CreateBookService svc = new CreateBookService();
  JsonServiceArgument arg = svc.getArgumentInstance();
  arg.jsonElement = jsonData;

  // perform the service
  ServiceHandler serviceHandler = RestfulStrolchComponent.getInstance().getServiceHandler();
  JsonServiceResult result = serviceHandler.doService(cert, svc, arg);

  // return depending on the result state
  if (result.isOk())
    return ResponseUtil.toResponse(StrolchRestfulConstants.DATA, result.getResult());
  return ResponseUtil.toResponse(result);
}
```

{{% notice tip %}}
Note: We return the created object again as JSON in its own data field.
{{% /notice %}}

The service is implemented as follows: 

```java
public class CreateBookService extends AbstractService<JsonServiceArgument, JsonServiceResult> {

  @Override
  protected JsonServiceResult getResultInstance() {
    return new JsonServiceResult();
  }

  @Override
  public JsonServiceArgument getArgumentInstance() {
    return new JsonServiceArgument();
  }

  @Override
  protected JsonServiceResult internalDoService(JsonServiceArgument arg) throws Exception {

    // open a new transaction, using the realm from the argument, or the certificate
    Resource book;
    try (StrolchTransaction tx = openArgOrUserTx(arg)) {

      // get a new book "instance" from the template
      book = tx.getResourceTemplate(BookShopConstants.TYPE_BOOK);

      // map all values from the JSON object into the new book element
      book.accept(new FromFlatJsonVisitor(arg.jsonElement.getAsJsonObject()).ignoreBag(BAG_RELATIONS));

      // save changes
      tx.add(book);

      // notify the TX that it should commit on close
      tx.commitOnClose();
    }

    // map the return value to JSON
    JsonObject result = book.accept(new StrolchElementToJsonVisitor().flat());

    // and return the result
    return new JsonServiceResult(result);
  }
}
```


{{% notice tip %}}
Note: For the authenticated user to be able to perform this service, we must add it to their privileges:
{{% /notice %}}

```xml
...
  <Role name="User">
    ...
    <Privilege name="li.strolch.service.api.Service" policy="DefaultPrivilege">
      <Allow>li.strolch.bookshop.service.CreateBookService</Allow>
    </Privilege>
    ...
  </Role>
...
```

## Update

Updating of a book is basically the same as the creation, we just use PUT,
verify that the book exists and give the user the privilege.

**PUT Method:**

```java
@PUT
@Path("{id}")
@Produces(MediaType.APPLICATION_JSON)
public Response update(@Context HttpServletRequest request, @PathParam("id") String id, String data) {

  // this is an authenticated method call, thus we can get the certificate from the request:
  Certificate cert = (Certificate) request.getAttribute(StrolchRestfulConstants.STROLCH_CERTIFICATE);

  // parse data to JSON
  JsonObject jsonData = JsonParser.parseString(data).getAsJsonObject();

  // instantiate the service with the argument
  UpdateBookService svc = new UpdateBookService();
  JsonServiceArgument arg = svc.getArgumentInstance();
  arg.objectId = id;
  arg.jsonElement = jsonData;

  // perform the service
  ServiceHandler serviceHandler = RestfulStrolchComponent.getInstance().getServiceHandler();
  JsonServiceResult result = serviceHandler.doService(cert, svc, arg);

  // return depending on the result state
  if (result.isOk())
    return ResponseUtil.toResponse(StrolchRestfulConstants.DATA, result.getResult());
  return ResponseUtil.toResponse(result);
}
```

**Update Service:**

```java
public class UpdateBookService extends AbstractService<JsonServiceArgument, JsonServiceResult> {

  @Override
  protected JsonServiceResult getResultInstance() {
    return new JsonServiceResult();
  }

  @Override
  public JsonServiceArgument getArgumentInstance() {
    return new JsonServiceArgument();
  }

  @Override
  protected JsonServiceResult internalDoService(JsonServiceArgument arg) throws Exception {

    // verify same book
    DBC.PRE.assertEquals("ObjectId and given Id must be same!", arg.objectId,
        arg.jsonElement.getAsJsonObject().get(Json.ID).getAsString());

    // open a new transaction, using the realm from the argument, or the certificate
    Resource book;
    try (StrolchTransaction tx = openArgOrUserTx(arg)) {

      // get the existing book
      book = tx.getResourceBy(BookShopConstants.TYPE_BOOK, arg.objectId, true);

      // map all values from the JSON object into the new book element
      book.accept(new FromFlatJsonVisitor(arg.jsonElement.getAsJsonObject()).ignoreBag(BAG_RELATIONS));

      // save changes
      tx.update(book);

      // notify the TX that it should commit on close
      tx.commitOnClose();
    }

    // map the return value to JSON
    JsonObject result = book.accept(new StrolchElementToJsonVisitor().flat());

    // and return the result
    return new JsonServiceResult(result);
  }
}
```

**Privilege**

```xml
...
  <Role name="User">
    ...
    <Privilege name="li.strolch.service.api.Service" policy="DefaultPrivilege">
      ...
      <Allow>li.strolch.bookshop.service.UpdateBookService</Allow>
      ...
    </Privilege>
    ...
  </Role>
...
```

## Remove

To remove a book, we need a DELETE method, a remove service and the associated
privilege.

**DELETE Method:**

```java
@DELETE
@Path("{id}")
@Produces(MediaType.APPLICATION_JSON)
public Response update(@Context HttpServletRequest request, @PathParam("id") String id) {

  // this is an authenticated method call, thus we can get the certificate from the request:
  Certificate cert = (Certificate) request.getAttribute(StrolchRestfulConstants.STROLCH_CERTIFICATE);

  // instantiate the service with the argument
  RemoveBookService svc = new RemoveBookService();
  StringServiceArgument arg = svc.getArgumentInstance();
  arg.value = id;

  // perform the service
  ServiceHandler serviceHandler = RestfulStrolchComponent.getInstance().getServiceHandler();
  ServiceResult result = serviceHandler.doService(cert, svc, arg);

  // return depending on the result state
  return ResponseUtil.toResponse(result);
}
```

**Remove Service:**

```java
public class RemoveBookService extends AbstractService<StringServiceArgument, ServiceResult> {

  @Override
  protected ServiceResult getResultInstance() {
    return new ServiceResult();
  }

  @Override
  public StringServiceArgument getArgumentInstance() {
    return new StringServiceArgument();
  }

  @Override
  protected ServiceResult internalDoService(StringServiceArgument arg) throws Exception {

    // open a new transaction, using the realm from the argument, or the certificate
    try (StrolchTransaction tx = openArgOrUserTx(arg)) {

      // get the existing book
      Resource book = tx.getResourceBy(BookShopConstants.TYPE_BOOK, arg.value, true);

      // save changes
      tx.remove(book);

      // notify the TX that it should commit on close
      tx.commitOnClose();
    }

    // and return the result
    return ServiceResult.success();
  }
}
```

**Privilege:**

```xml
...
  <Role name="User">
    ...
    <Privilege name="li.strolch.service.api.Service" policy="DefaultPrivilege">
      ...
      <Allow>li.strolch.bookshop.service.RemoveBookService</Allow>
      ...
    </Privilege>
    ...
  </Role>
...
```

## Notes

One should now see a pattern emerge:

* The REST API delegates to the Services, or Searches, with the exception of the
  retrieval of a single object by id.
* Services should do initial validation of the input. Not much validation was
  done here, but more could be done.
* Commands are reusable objects to perform recurring work.
* Searches and Services are privileged actions for which a user must have the
  privilege to perform the action.

The book services are quite simple, but as more requirements arise, it should be
easy to implement them in the service layer. Thus should a service be required
to be performed by an integration layer, then they can simply call the services,
since the input is defined and validation is done there (i.e. NOT in the REST
API).

This concludes the CRUD of books.

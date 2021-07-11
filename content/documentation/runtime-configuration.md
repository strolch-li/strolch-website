---
title: 'Runtime Configuration'
weight: 40
---

## Runtime Configuration
A Strolch runtime configuration comprises two parts: a configuration part, and 
a model part. The configuration are files located in the `..config/` folder, 
and the model are files located in the `../data` folder.

In an absolute minimal configuration, the Strolch runtime requires the 
following folder structure:

* `../config/`
  * `../StrolchConfiguration.xml` → configures the Strolch agent
  *  `../PrivilegeConfig.xml` → configures user management
  *  `../PrivilegeUsers.xml` → contains the users in an XML based user management file
  *  `../PrivilegeRoles.xml` → contains the roles and privileges in an XML based user management

## StrolchConfiguration.xml

The StrolchConfiguration.xml file configures the Strolch agent. The StrolchConfiguration.xml defines the following:

* `<StrolchConfiguration>` root element
* `<env id="xxx">` different environments with the possibility of having a 
  global environment for configuration valid in multiple environments.
  *  `<Runtime>` element which defines the agents name and a few other 
     properties e.g. `locale` and `verbose`:
    * `<applicationName>` the agent's name
    * `<Properties>`
      * `<locale>` the agent's internal locale for log messages etc.
      * `<verbose>` the logging level for some internal logging. (Logging is 
        mostly done using log4j over slf4j)
  * `<Component>` elements for each component used in the agent. A component 
    is configured by defining the following child elements:
    * `<name>` the name of the component, use when defining dependencies 
      between components. The name is mostly set to the simple name of the 
      interface of the component
    * `<api>` the full class name to the interface of the component. During 
      runtime this interface will be used to access the component e.g.: 
      
      `ServiceHandler svcHandler = agent.getContainer().getComponent(ServiceHandler.class);`
    * `<impl>` the full class name of the concrete implementation of the 
      component. During initialization this class will be instantiated and 
      registered under the component name and interface. This class must 
      extend the class li.strolch.agent.api.StrolchComponent
    * `<depends>` any number of these elements, where the content is the name 
      of another component, on which this component depends. Depending 
      components are initialized and started after the component they depend 
      on and are stopped and destroyed before
    * `<Properties>`
    * `<...>` any number of properties which the component requires. The 
      element's name will be the key with which the value can be accessed at 
      runtime.

{{% notice warning %}}
When a property is missing, and the component has a hard coded default value, then when the component is initialized, the use of the default value and its key is logged. This makes it easy to see which new properties can be configured. Should the component not define a default value, then the component will thrown an exception on initialization. In this case it can be a good moment to read the JavaDoc (or source code) for the component in question to see how it is configured.
{{% /notice %}}

## Privilege Configuration
In Strolch authentication and authorization is baked in. To open a transaction, 
and thus access the Strolch model, a Certificate object is required, which 
means the user has been authenticated and possibly authorized.

The PrivilegeConfig.xml defines the following:

* `<Privilege>` root element
  * `<Container>` configures the individual Privilege components
    * `<Parameters>` base configuration properties for Privilege
    * `<EncryptionHandler>` configures the hashing algorithms and other 
      encryption specific configuration
    * `<PersistenceHandler>` configures the persistence of the roles and users
    * `<UserChallengeHandler>` configures a challenge handler so that a user 
      can reset their password. The default challenge handler is the 
      `li.strolch.privilege.handler.MailUserChallengeHandler` which sends a 
      challenge to the user's defined e-mail address.
    * `<SsoHandler>` the SSO Handler is used to implement a SingleSignOn and 
      can be used to start a session using a LDAP token, etc. There is no 
      default implementation as this is project specific.
  * `<Policies>` configures the available privilege policies at runtime, the 
    name is referenced from the model file

The `PrivilegeUsers.xml` and `PrivilegeRoles.xml` define the users and roles 
and is used when in `PrvilegeConfig.xml` the `PersistenceHandler` is set to 
`ch.eitchnet.privilege.handler.XmlPersistenceHandler`:

* `<Users>` configures all users
  * `<User>` configures a specific user
    * `<Firstname>` configures a user's first name
    * `<Lastname>` configure a user's last name
    * `<State>` configures the user's state, see `li.strolch.privilege.model.UserState`
    * `<Locale>` configure the user's locale
    * `<Roles>` configures the user's roles
      * `<Role>` adds a role to the user
      * `<Properties>` configures user specific properties. What properties 
        are used is not specified and is dependent on the concrete agent
        * `<Property>` defines a single property
  * `<Roles>` configures all roles
    * `<Role>` configures a specific role
      * `<Privilege>` configures a specific privilege for this role
        * `<AllAllowed>` if set to true, then defines that all values 
          associated with this privilege are allowed
        * `<Allow>` defines one allowed value for this privilege
        * `<Deny>` defines one denied value for this privilege

## Implementing a StrolchComponent
Implementing a strolch component requires an interface, which defines the 
component's API and a concrete class which implements the interface and 
extends the class `StrolchComponent`.

The StrolchComponent class adds the state model to the class, which 
transitions as follows:

`UNDEFINED => SETUP => INITIALIZED => STARTED <=> STOPPED => DESTROYED`

Components can switch between `STARTED` and `STOPPED`, but once `DESTROYED` no 
further state change is possible. The component's state is changed by changes 
to the agent's lifecycle.

A component's state is changed by a call to the appropriate method on the 
component, override the methods as necessary. Note that it is good practice 
that the `initialize()`-method is used to get all the configuration properties, 
and that they should there be evaluated and that the method so return quickly. 
The `start()`-method is called after the agent's initialization and should be 
where additional threads are started. Correctly implementing these methods 
allows to quickly detect a wrongly configured agent, which might take longer 
to start for whatever reason.

The following shows a basic implementation of a component on the basis of a 
post initializer (a component which performs some actions in its 
`start()`-method which should be done after everything else is started in the 
agent).

```java
public class SimplePostInitializer
        extends StrolchComponent
        implements PostInitializer {

  public SimplePostInitializer(ComponentContainer container,
        String componentName) {
    super(container, componentName);
  }

  @Override
  public void initialize(ComponentConfiguration configuration) {
    // do some initialization, validate configuration values, etc.
    // now call super, to update state
    super.initialize(configuration);
  }

  @Override
  public void start() {
    // start any threads, or perform long running start work
    // now call super, to update state
    super.start();
  }

  @Override
  public void stop() {
    // stop threads and timers, but be ready to start again
    // now call super, to update state
    super.stop();
  }

  @Override
  public void destroy() {
    // destroy this component, release all resources and don't worry about
    // being called to start again now call super, to update state
    super.destroy();
  }
}
```

The new component would then be registered in the `StrolchConfiguration.xml` 
as follows:

```xml
<StrolchConfiguration>
  <env id="...">
    ...
    <Component>
      <name>SimplePostInitializer</name>
      <api>li.strolch.agent.api.PostInitializer</api>
      <impl>li.strolch.documentation.SimplePostInitializer</impl>
    </Component>
    ...
  </env>
</StrolchConfiguration>
```

And can be access at runtime using:

```java
PostInitializer postInitializer = getContainer().getComponent(PostInitializer.class);
```

## Starting the agent
When a Strolch runtime is started, then the root path to the runtime configuration must be passed. In Java this is done by calling:

```java
StrolchAgent agent = new StrolchAgent();
agent.setup(environment, rootPath);
agent.initialize();
agent.start();
```

In Servlet 3.0 applications one would implement the 
`javax.servlet.ServletContextListener` interface, add the `@WebListener` 
annotation to the class and in the `contextInitialized()`-method start Strolch:

```java
String realPath = sce.getServletContext().getRealPath("/WEB-INF");
String environment = StrolchEnvironment.getEnvironmentFromEnvProperties(pathF);
this.agent = new StrolchAgent();
this.agent.setup(environment, new File(realPath));
this.agent.initialize();
this.agent.start();
```

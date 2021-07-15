---
title: 'Components'
weight: 60
---

## Components
A Strolch agent can be easily extended with arbitrary components. An agent 
is basically a container for classes extending StrolchComponent with a life 
cycle. These classes mostly implement an interface which describes the 
operations that are supported by the component.

The following represents a list of the most used components:
* `RealmHandler`: li.strolch.agent.impl.DefaultRealmHandler
* `PrivilegeHandler`: li.strolch.runtime.privilege.DefaultStrolchPrivilegeHandler
* `EnumHandler`: li.strolch.runtime.query.enums.DefaultEnumHandler
* `PolicyHandler`: li.strolch.policy.DefaultPolicyHandler
* `ServiceHandler`: li.strolch.service.api.DefaultServiceHandler
* `StrolchSessionHandler`: li.strolch.rest.DefaultStrolchSessionHandler
* `PersistenceHandler`: multiple implementations
* `PostInitializer`: project specific implementation
* `MailHandler`: li.strolch.handler.mail.SmtpMailHandler

A component has a life-cycle, which is governed by the Agent's own life-cycle. 
The life-cycle is as follows:

```
setup -> initialize -> start <-> stop -> destroy
```

The setup step is used to instantiate the component, the initialize step is 
used to validate configuration parameters, and the run step is used to start 
the component, i.e. start threads, etc. The stop step stops these threads and 
also allows the component to be started again. The destroy step destroys the 
instance and makes it unusable anymore, i.e. shutdown of the agent.

Each component has its own configuration parameters. A component is 
registered in the `StrolchConfiguration.xml` file with a
* name
* api class name
* implementation class name
* configuration parameters
* any required dependencies

The dependencies is an important feature as the dependencies of a component 
are always started before the actual component.

## Example Component implementation and configuration

By example of the `MailHandler` we shall show how a strolch component would 
be implemented.

First define an interface:
```java
public interface MailHandler {
    void sendMail(String subject, String text, String recipient);
}
```

Then implement a concrete `MailHandler`:
```java
public class SmtpMailHandler extends StrolchComponent implements MailHandler {

  // TODO instance fields with configuration properties to send the mail

  public SmtpMailHandler(ComponentContainer container, String componentName) {
    super(container, componentName);
  }

  @Override
  public void initialize(ComponentConfiguration configuration) throws Exception {

    // TODO store any properties needed from the configuration

    super.initialize(configuration);
  }

  @Override
  public void sendMail(String subject, String text, String recipient) {

    // TODO send the e-mail using SMTP, or send asynchronously
  }
}
```

Now that the component is written, it must be registered on the component, so 
that it is loaded when the agent is started. For this the 
`StrolchConfiguration.xml` file must be modified to include a component 
element:
```xml
<StrolchConfiguration>
  <env id="dev">
    ...
    <Component>
      <name>MailHandler</name>
      <api>li.strolch.handler.mail.MailHandler</api>
      <impl>li.strolch.handler.mail.SmtpMailHandler</impl>
      <Properties>
        <username>test</username>
        <password>test</password>
        <hostName>localhost</hostName>
        ...
      </Properties>
    </Component>
    ...
  </env>
</StrolchConfiguration>
```

Now when the agent is started, the component can be retrieved and used. 
E.g from inside a Service:
```java
public class MyService extends AbstractService<ServiceArgument, ServiceResult> {
	@Override
	protected ServiceResult internalDoService(ServiceArgument arg) throws Exception {
		MailHandler mailHandler = getComponent(MailHandler.class);
		mailHandler.sendMail("My Subject", "Hello World", "test@test.ch");
    }
}
```


---
title: 'API'
weight: 10
---

## Overview
The Strolch API revolves around the StrolchTransaction object. The main 
concept is to implement your use cases in Service implementations. You 
open a transaction using the openTx(String)-method and then perform the 
use case by adding your Command instances to the transaction.

Transactions are opened on a StrolchRealm. The realms are used to 
separate mandates in a single runtime instance of Strolch. Each realm 
has its own ResourceMap, OrderMap, ActivityMap instances from which the 
TX retrieves the elements.

## Model
The Strolch model is implemented in the project li.strolch.model.

The Strolch model consists of three root level elements: Resource, 
Order and Activity. Each element has at least the following attributes:

* Id → the element's id
* Name → the element's name
* Type → the element's type

Each root element can have any number of ParameterBag instances on it, 
which in turn can have any number of Parameters on it. Accessing these 
objects is always done by their IDs. Strolch root elements are always 
stored in the respective ElementMaps in their Strolch realm. Thus 
accessing a certain parameter from a Resource would look like this:

```java
public class Test {
  public static void main(String[] args) {
    try (StrolchTransaction tx = openTx(realmName)) {
      Resource resource = tx.getResourceBy("MyType", "myResource");
      Date date = resource.getDate("myBag", "myParam1");
      logger.info("myParam date has value " + date);
    }
  }
}
```

XML Presentation of Strolch's top level elements of Resources: 

```xml
<!-- Resource instance -->
<Resource Id="myResource" Name="Test Name" Type="MyType">
  <ParameterBag Id="parameters" Name="Parameters" Type="Parameters">
    <Parameter Id="myParam2" Name="StringList Param" Type="StringList" Value="Hello, World" />
    <Parameter Id="myParam1" Name="Date Param" Type="Date" Value="2012-11-30T18:12:05.628+01:00" />
    <Parameter Id="myParam3" Name="String Param" Type="String" Value="Strolch" />
  </ParameterBag>
  <ParameterBag Id="additionalParameters" Name="Parameters" Type="Parameters">
    <Parameter Id="myParam1" Name="Long Param" Type="Long" Value="4453234566" />
    <Parameter Id="myParam2" Name="Integer Param" Type="Integer" Value="77" />
    <Parameter Id="myParam3" Name="Float Param" Type="Float" Value="44.3" />
    <Parameter Id="myParam4" Name="Boolean Param" Type="Boolean" Value="true" />
  </ParameterBag>
  <TimedState Id="myState" Name="Integer State" Type="IntegerState">
    <Value Time="0" Value="1" />
    <Value Time="1" Value="2" />
    <Value Time="2" Value="3" />
  </TimedState>
</Resource>
```

XML Presentation of Strolch's top level elements of Orders:

```xml
<!-- Order instance -->
<Order Id="myOrder" Name="Test Name" Type="MyOrderType" Date="2013-11-20T07:42:57.699Z" State="CREATED">
  <ParameterBag Id="parameters" Name="Parameters" Type="Parameters">
    <Parameter Id="myParam2" Name="StringList Param" Type="StringList" Value="Hello, World" />
    <Parameter Id="myParam1" Name="Date Param" Type="Date" Value="2012-11-30T18:12:05.628+01:00" />
    <Parameter Id="myParam3" Name="String Param" Type="String" Value="Strolch" />
  </ParameterBag>
  <ParameterBag Id="additionalParameters" Name="Parameters" Type="Parameters">
    <Parameter Id="myParam1" Name="Long Param" Type="Long" Value="4453234566" />
    <Parameter Id="myParam2" Name="Integer Param" Type="Integer" Value="77" />
    <Parameter Id="myParam3" Name="Float Param" Type="Float" Value="44.3" />
    <Parameter Id="myParam4" Name="Boolean Param" Type="Boolean" Value="true" />
  </ParameterBag>
</Order>
```

XML Presentation of Strolch's top level elements of Activities:

```xml
<!-- Activity instance -->
<Activity Id="bicycleProduction" Name="Bicycle Production" Type="Series">
  <Activity Id="componentProduction" Name="Production of components" Type="Series">
    <Action Id="consumeGears" Name="Gears" ResourceId="gears" ResourceType="Article" Type="Consume">
      <ParameterBag Id="objectives" Name="Production goals" Type="Objectives">
        <Parameter Id="quantity" Name="Quantity" Type="Float" Value="1"/>
        <Parameter Id="duration" Name="Duration" Type="Duration" Value="PT0S"/>
      </ParameterBag>
    </Action>
    <Activity Id="frameProduction" Name="Production frame" Type="Series">
      <Action Id="produce" Name="Production frame" ResourceId="frameProduction" ResourceType="Machine" Type="Use">
        <ParameterBag Id="objectives" Name="Production goals" Type="Objectives">
          <Parameter Id="quantity" Name="Quantity" Type="Float" Value="1"/>
          <Parameter Id="duration" Name="Duration" Type="Duration" Value="PT5M"/>
        </ParameterBag>
      </Action>
      <Action Id="toStock" Name="Frame ToStock" ResourceId="frame" ResourceType="Article" Type="Produce">
        <ParameterBag Id="objectives" Name="Production goals" Type="Objectives">
          <Parameter Id="quantity" Name="Quantity" Type="Float" Value="1"/>
          <Parameter Id="duration" Name="Duration" Type="Duration" Value="PT1M"/>
        </ParameterBag>
      </Action>
    </Activity>
    <Activity Id="brakeProduction" Name="Herstellen Bremsen" TimeOrdering="Series" Type="Series">
      <Action Id="produce" Name="Production saddle" ResourceId="saddleProduction" ResourceType="Machine" Type="Use">
        <ParameterBag Id="objectives" Name="Production goals" Type="Objectives">
          <Parameter Id="quantity" Name="Quantity" Type="Float" Value="1"/>
          <Parameter Id="duration" Name="Duration" Type="Duration" Value="PT5M"/>
        </ParameterBag>
      </Action>
      <Action Id="toStock" Name="Saddle ToStock" ResourceId="frame" ResourceType="Article" Type="Produce">
        <ParameterBag Id="objectives" Name="Production goals" Type="Objectives">
          <Parameter Id="quantity" Name="Quantity" Type="Float" Value="1"/>
          <Parameter Id="duration" Name="Duration" Type="Duration" Value="PT1M"/>
        </ParameterBag>
      </Action>
    </Activity>
  </Activity>
  <Action Id="assembly" Name="Bicycle assemble" ResourceId="bicycleAssembly" ResourceType="Assembly" Type="Use">
    <ParameterBag Id="objectives" Name="Production goals" Type="Objectives">
      <Parameter Id="quantity" Name="Quantity" Type="Float" Value="1"/>
      <Parameter Id="duration" Name="Duration" Type="Duration" Value="PT5M"/>
    </ParameterBag>
  </Action>
  <Action Id="toStock" Name="Bicycle to stock" ResourceId="bicycle" ResourceType="Product" Type="Produce">
    <ParameterBag Id="objectives" Name="Production goals" Type="Objectives">
      <Parameter Id="quantity" Name="Quantity" Type="Float" Value="1"/>
      <Parameter Id="duration" Name="Duration" Type="Duration" Value="PT1M"/>
    </ParameterBag>
  </Action>
</Activity>
```

## Realms
Strolch realms implement the multi-client capability which is thus baked right 
into the Strolch runtime. When configuring a Strolch runtime, realms are 
configured and for each realm the data store mode is set. Each realm has its 
own persistence configuration and can thus run in one of the 4 modes that the 
Strolch agent implements:

* EMPTY
  This is a transient data store mode, where no model changes are persisted, 
  but they are only kept in memory. When the Strolch agent is started, this 
  realm stays empty as no data is loaded.
* TRANSIENT 
  This is the same as EMPTY, but with the difference that when the Strolch 
  agent is started, an XML file is parsed and the in memory realm is populated 
  with the elements parsed from that XML file.
* CACHED 
  In this mode, all data is stored in memory, and any changes made are written 
  back to the persistence layer. This allows for fast in-memory quries, but 
  makes sure no data is lost when the agent is restarted.

Strolch Realms are also responsible for opening Transactions, as these are bound
to the persistence layer configured for this realm. At runtime, a realm is then
accessed from the ComponentContainer:

```java
public class Example {
  public static void main(String[] args) {
    ComponentContainer container = getAgent().getContainer();
    StrolchRealm realm = container.getRealm(StrolchConstants.DEFAULT_REALM);
    try (StrolchTransaction tx = realm.openTx()) {
      Resource resource = tx.getResourceBy("TestType", "MyTestResource");
    }
  }
}
```

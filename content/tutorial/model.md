---
title: 'Model'
weight: 20
---

## Model

Looking back at our functionality, we can list the following entities that need
to be modelled (We'll go into detail further down):

* Book → books can be orderd
* UserCart → we want to store the cart of the user
* Account → we need to know where to send the orders
* PurchaseOrder → we need to know what was ordered and keep track of its state
* FromStock → we want to use activities to implement the process of an order

In Strolch we model entities by defining the element as a template. Thus in the
`templates.xml` file we can add the templates with the following content:

**Book**

```xml

<Resource Id="Book" Name="Book Template" Type="Template">
  <ParameterBag Id="parameters" Name="Parameters" Type="Parameters">
    <Parameter Id="description" Name="Description" Type="String" Value=""/>
    <Parameter Id="quantity" Name="Quantity in Stock" Type="Integer" Value="0"/>
  </ParameterBag>
</Resource>
```

**Account**

```xml

<Resource Id="Account" Name="Account Template" Type="Template">
  <ParameterBag Id="parameters" Name="Parameters" Type="Parameters">
    <Parameter Id="user" Name="User" Type="String" Value=""/>
    <Parameter Id="firstName" Name="First Name" Type="String" Value=""/>
    <Parameter Id="lastName" Name="Last Name" Type="String" Value=""/>
    <Parameter Id="email" Name="E-Mail" Type="String" Value=""/>
  </ParameterBag>
  <ParameterBag Name="Address" Id="address" Type="Address">
    <Parameter Id="phone" Name="Telephone Number" Type="String" Value=""/>
    <Parameter Id="street" Name="Street" Type="String" Value=""/>
    <Parameter Id="city" Name="City" Type="String" Value=""/>
    <Parameter Id="zip" Name="Postal Code" Type="String" Value=""/>
    <Parameter Id="country" Name="Country" Type="String" Value=""/>
  </ParameterBag>
</Resource>
```

**UserCart**

```xml

<Resource Id="UserCart" Name="UserCart Template" Type="Template">
  <ParameterBag Id="books" Name="Books" Type="Book">
    <!-- Parameter Id="bookId" Name="Book reference" Type="Float" Value="0" / -->
  </ParameterBag>
  <ParameterBag Id="relations" Name="Relations" Type="Parameters">
    <Parameter Id="account" Name="Account" Type="String"
               Interpretation="Resource-Ref" Uom="Account" Value=""/>
  </ParameterBag>
</Resource>
```

**PurchaseOrder**

```xml

<Order Id="PurchaseOrder" Name="PurchaseOrder Template" Type="Template"
       State="Created">
  <ParameterBag Id="books" Name="Books" Type="Book">
    <!-- Parameter Id="bookId" Name="Book reference" Type="Float" Value="0" / -->
  </ParameterBag>
  <ParameterBag Id="relations" Name="Relations" Type="Parameters">
    <Parameter Id="account" Name="Account" Type="String"
               Interpretation="Resource-Ref" Uom="Account" Value=""/>
  </ParameterBag>
</Order>
```

**FromStock**

```xml

<Activity Id="FromStock" Name="From Stock Template" Type="FromStock"
          TimeOrdering="Series">
  <ParameterBag Name="objectives" Id="Objectives" Type="Objectives">
    <Parameter Name="Duration" Id="duration" Value="PT1MS" Type="Duration"/>
  </ParameterBag>

  <Action Id="validate" Name="Validation of order" Type="Use"
          ResourceType="Validation" ResourceId="validation"/>

  <!-- for each book we do a consume, i.e. reduce the stock quantity -->
  <Action Id="Consume" Name="Consume Template for book" Type="Template">
    <ParameterBag Id="parameters" Name="Parameters" Type="Parameters">
      <Parameter Id="quantity" Name="Quantity" Type="Float" Value="0"/>
    </ParameterBag>
  </Action>

  <Action Id="package" Name="Packaging of PurchaseOrder" Type="Use"
          ResourceType="Packaging" ResourceId="packaging"/>
  <Action Id="send" Name="Sending of package" Type="Use" ResourceType="Sending"
          ResourceId="sending"/>

</Activity>

```

Let's explain a few things:

* The `Book` entity is a `Resource` object and only contains the description and the
  current quantity in stock.
* The `Account` entity is a Resource and contains the address and further details
  of the user, and with the `user` parameter the username is defined, thus
  referencing the real user.
* The `UserCart` entity is a Resource and has a reference to the `account` Resource.

  {{% notice tip %}}
  Note how the reference is done using a StringParameter, where `Interpretation`,
  `UOM` and the `value` is set in a specific manner.
  {{% /notice %}}
  
* The `UserCart` entity is a Resource and references `books` using a special
  ParameterBag with the type set to `Book`, the actual type of the book entity.
  Each Parameter is of type Float and the ID of the parameter is the ID of the
  book, and the value is the quantity that the user would like to purchase.
  
  There will only be one cart per user/account.
  
* The `PurchaseOrder` entity is an `Order` object, and is basically a copy of the
  UserCart entity. This is the confirmed purchase order for the contents of a
  cart, and can then be used for reports on how much of which book was sold.
* The `FromStock` entity is an `Activity` object and defines the process we will go
  through when delivering a purchase to a user. 
  
  {{% notice tip %}}
  Note how the activity has a
  ParameterBag `objectives` with a `duration` parameter. This defines for
  this activity how long each `Action` should execute. This can be overridden in
  each Action and can help to plan how much effort goes into the delivering of
  each PurchaseOrder.
  {{% /notice %}}

  {{% notice tip %}}
  Further note how the activity has three special actions (`validate`, `package` and
  `send`) on which a `ResourceType` and `ResourceId` are defined. Actions are always
  performed on a Resource, as the referenced Resource defines the behaviour of
  the action through defined `Policy` objects.
  {{% /notice %}}
  
* For each book which will be purchased, an Action will be created of type
  `Consume`. In the template this is defined by a template `Action` with the id
  `Consume` and will later be changed accordingly.

Since we are referencing resources from actions in the activity, we need to add
these as well, but not as templates. They can be added to the `defaultModel.xml`
file:

```xml
<Resource Id="validation" Name="Validation Resource" Type="Validation">
  <Policies>
    <Policy Type="ExecutionPolicy" Value="key:ValidationExecution" />
    <Policy Type="ConfirmationPolicy" Value="key:DefaultConfirmation" />
  </Policies>
</Resource>

<Resource Id="packaging" Name="Packaging Resource" Type="Packaging">
  <Policies>
    <Policy Type="ExecutionPolicy" Value="key:PackagingExecution" />
    <Policy Type="ConfirmationPolicy" Value="key:DefaultConfirmation" />
  </Policies>
</Resource>

<Resource Id="sending" Name="Sending Resource" Type="Sending">
  <Policies>
    <Policy Type="ExecutionPolicy" Value="key:SendingExecution" />
    <Policy Type="ConfirmationPolicy" Value="key:DefaultConfirmation" />
  </Policies>
</Resource>
```

What should now be noted by these three new Resources is that they have Policy
definitions:

* `ExecutionPolicy` → defines how an action on this resource is executed by
  referencing an ExecutionPolicy implementation.
* `ConfirmationPolicy` → defines behaviour to be performed on every state change
  of an action being performed on this resource by referencing an
  ConfirmationPolicy implementation.

Currently these resources reference policies which don't exist. We will resolve
this issue later, when we implement the execution of the activity.

This concludes the model definition. In the next step we'll start creating
services and commands for our model.





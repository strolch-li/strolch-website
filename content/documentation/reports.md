---
title: 'Reports'
weight: 130
---

## Reports

Since Strolch has a generic model, it was rather straight forward to create a
simple API for writing reports. In Strolch a report is defined by using its own
model, i.e. a Report is a `Resource` of type `Report`.

A report consists of the following parts:

* policy definition, thus allowing extensions
* basic configuration like base object type, order direction, etc.
* column definitions
* joins
* ordering definition
* filters

An example of a report is as follows:

```xml

<Resource Id="stockReport" Name="Stock Report" Type="Report">

    <ParameterBag Id="parameters" Name="parameters" Type="Parameters">
        <Parameter Id="objectType" Index="20" Hidden="false" Name="Object Type"
                   Type="String" Interpretation="Resource-Ref" Uom="Player"
                   Value="Player"/>
        <Parameter Id="descending" Name="Descending order" Type="Boolean"
                   Value="true"/>
    </ParameterBag>

    <ParameterBag Id="ordering" Name="Ordering" Type="Ordering">
        <Parameter Id="name" Name="Name" Type="String"
                   Interpretation="Resource-Ref" Uom="Player" Value="$name"/>
    </ParameterBag>

    <ParameterBag Id="noTeamFilter" Name="Filter" Type="Filter">
        <Parameter Id="policy" Name="Filter Policy" Type="String"
                   Interpretation="ReportFilterPolicy" Uom="key:Equals"
                   Value="!"/>
        <Parameter Id="fieldRef" Name="Field reference" Type="String"
                   Interpretation="Resource-Ref" Uom="Slot"
                   Value="Bags/relations/team"/>
    </ParameterBag>

    <ParameterBag Id="columns" Name="Display Columns" Type="Display">
        <Parameter Id="name" Name="Player" Type="String"
                   Interpretation="Resource-Ref" Uom="Player" Value="$name"/>
        <Parameter Id="birthDate" Name="Birth date" Type="String"
                   Interpretation="Resource-Ref" Uom="Player"
                   Value="Bags/parameters/birthDate"/>
        <Parameter Id="team" Name="Team" Type="String"
                   Interpretation="Resource-Ref" Uom="Team" Value="$name"/>
    </ParameterBag>

    <ParameterBag Id="joins" Name="Joins" Type="Joins">
        <Parameter Id="Team" Index="10" Hidden="false" Name="Team" Type="String"
                   Interpretation="Resource-Ref" Uom="Team" Value="Player"/>
    </ParameterBag>

    <Policies>
        <Policy Type="ReportPolicy"
                Value="java:li.strolch.report.policy.GenericReport"/>
    </Policies>

</Resource>
```

This report

* shows all Resources of type player (parameter `objectType`) → marks the object
  type to be show in the filter criteria (default), and that its sorting index
  is at 20.
* orders the report by player's name (parameter bag `ordering`)
* filters out all players with no team assigned (parameter bag `noTeamFilter`)
* defines three columns: Player, Birth date, Team (paramger bag `columns`)
* joins in the resource of type `Team`
* Uses the `GenericReport` class to generate the report

## GenericReport

The default generic report implemented in Strolch has the following features and
options:

### Parameters

The parameters bag can contain the following parameters:

* `objectType` → the base type of object to get the input for the report. This
  means that the `Interpretation` is set to one of:
  
    * `Resource-Ref`
    * `Order-Ref`
    * `Activity-Ref`

  and that the `UOM` and `value` of the parameter is set to the type of element with
  which to retrieve the elements from the strolch model.
* `descending` → boolean flag to define if sorting is in descending order
* `allowMissingColumns` → flag to define if no exception should be thrown if a
  column is missing
* `dateRangeSel` → defines a lookup parameter to use as a date range selector.
  This requires input when executing the report

{{% notice warning %}}
Note: that the attributes Hidden and Index define the
visibility and sorting index as filter criteria respectively.
{{% /notice %}}

### Lookups

Many of the features of the generic report rely on looking up a value on the
referenced element. The following shows the ways that a lookup can be performed:

* `$id` → lookup the ID of the element
* `$name` → lookup the name of the element
* `$type` → lookup the type of the element
* `$date` → lookup the date of the element (only possible on `Order`
  and `Activity` elements)
* `$state` → lookup the state of the element (only possible on `Order`
  and `Activity` elements)
* `Bags/<bag_id>/<param_id>` → a lookup on the selected element by bag ID and
  parameter ID
* `$search:<parent_ref_id>:Bags/<bag_id>/<param_id>` → searches for a parameter
  with the given bag and parameter, and if it does not exist, looks for the
  parent with the given parent_ref_id on the element. This allows a recursive
  search up a tree of elements which all have the same parameter referencing a
  parent. relations bag

{{% notice warning %}} Note: these definitions are set as the value of a
Parameter, and the Interpretation and UOM of the parameter is used to find the
element on which to perform the lookup. I.e. the following definition:
{{% /notice %}}

```xml
<Parameter Id="name" Name="Player" Type="String" Interpretation="Resource-Ref" Uom="Player" Value="$name"/>
```

defines that we want to lookup the name of the resource of type Player.

### Ordering

Ordering, i.e sorting is done by adding the parameter bag with the id `ordering`
and each parameter defines a column to order by. The sequence of the ordering is
defined by the `index` value assigned to each parameter.

### Filtering

Filtering use additional Strolch Policies which implement the operator function.
I.e. performing an equals, etc. The following `ReportFilterPolicy` are available
and should be added in your `StrolchPolicies.xml` file:

```xml
<StrolchPolicies>
  ...
  <PolicyType Type="ReportFilterPolicy" Api="li.strolch.report.policy.ReportFilterPolicy">
    <Policy Key="GreaterThan" Class="li.strolch.report.policy.GreaterThanReportFilter"/>
    <Policy Key="LessThan" Class="li.strolch.report.policy.LessThanReportFilter"/>
    <Policy Key="Equals" Class="li.strolch.report.policy.EqualsReportFilter"/>
    <Policy Key="Contains" Class="li.strolch.report.policy.ContainsReportFilter"/>
    <Policy Key="IsIn" Class="li.strolch.report.policy.IsInReportFilter"/>
    <Policy Key="ValueRef" Class="li.strolch.report.policy.ValueRefReportFilter"/>
  </PolicyType>
  ...
</StrolchPolicies>
```

From this we can see that we can perform a `GreaterThan`, `LessThan` and `Equals`
filtering. These filters can also be negated by prefixing the filter value with
an exclamation mark (!).

A special case for the filter values are filters on dates. If you are filtering
on a date, then you can use the special operator `now()`. This filter will use the
current date and time and will add/subtract the ISO8601 period passed as an
argument to the operator.

The following shows examples of these filters:

```xml
<ParameterBag Id="minQtyFilter" Name="Filter" Type="Filter">
  <Parameter Id="policy" Name="Filter Policy" Type="String" Interpretation="ReportFilterPolicy" Uom="key:GreaterThan" Value="10"/>
  <Parameter Id="fieldRef" Name="Field reference" Type="String" Interpretation="Resource-Ref" Uom="Product" Value="Bags/parameters/quantity"/>
</ParameterBag>

<ParameterBag Id="notEmptyFilter" Name="Filter" Type="Filter">
  <Parameter Id="policy" Name="Filter Policy" Type="String" Interpretation="ReportFilterPolicy" Uom="key:Equals" Value="!"/>
  <Parameter Id="fieldRef" Name="Field reference" Type="String" Interpretation="Resource-Ref" Uom="Team" Value="Bags/relations/team"/>
</ParameterBag>

<ParameterBag Id="threeMonthsAgoFilter" Name="Filter" Type="Filter">
  <Parameter Id="policy" Name="Filter Policy" Type="String" Interpretation="ReportFilterPolicy" Uom="key:LessThan" Value="now(-P3M)"/>
  <Parameter Id="fieldRef" Name="Field reference" Type="String" Interpretation="Resource-Ref" Uom="FromStock" Value="$date"/>
</ParameterBag>
```

{{% notice tip %}}
Note: One parameter defines which policy gets used and the `key:<name>` value
references a policy defined in the `StrolchPolicies.xml` file. Further the lookup
is defined in the `fieldRef` parameter.
{{% /notice %}}

### Joins

To add columns from data which is not on the element denoted by the base object
type, we can join further elements. This is done by adding the parameter bag
`joins` and then each parameter references an element to join. The joining is done
as follows:

* The `Intepretation` and `UOM` define which object we want to join, i.e. resource
  of type foo
* The value of the parameter defines the type of element on which to find the
  reference
* The join ordering is not relevant, as the tree is traversed accordingly
* At least one join must reference the base object type
* The lookup of the join is done by finding a parameter with any ID, which has
  the same `Interpretation` and `UOM` as the join definition
* The attributes `Hidden` and `Index` define the visibility and sorting index as
  filter criteria respectively.

Thus the following:
```xml
<ParameterBag Id="joins" Name="Joins" Type="Joins">
  <Parameter Id="Team" Index="10" Hidden="false" Name="Team" Type="String" Interpretation="Resource-Ref" Uom="Team" Value="Player"/>
  <Parameter Id="Country" Index="5" Hidden="false" Name="Team" Type="String" Interpretation="Resource-Ref" Uom="Country" Value="Team"/>
</ParameterBag>
```

Performs two joins: First we join a resource of type `Team` by finding the
relevant parameter on the `Player` resource, and then we lookup a resource of type
`Country` on the previously joined `Team` resource.

### Execution of Reports

To execute a reports, we must instantiate the Report and can then directly
generate a JsonObject stream, which we can then pipe to a browser, file, etc.:

```java
Stream<JsonObject> jsonObjectStream = new Report(tx, reportId).doReportAsJson();
```

If you prefer a CSV report:
```java
try (CSVPrinter csvP = new CSVPrinter(new OutputStreamWriter(out),
        CSVFormat.DEFAULT.withHeader(headers).withDelimiter(';'))) {

  // do report without AsJson, and then iterating each row and sending to a CSV writer
  report.doReport().forEach(row -> {
    try {
      csvP.printRecord(row.valueStream().collect(Collectors.toList())); // add to CSV
    } catch (Exception e) {
      logger.error("Could not write CSV row", e);
    }
  });
}
```

### Filter Criteria

Predefining filters is a good start, but in some case you only want a portion of
the actual filtered data. For instance if you make a stock report, you might
only want one location. This information is dynamic and thus not stored on the
report definition.

To perform these dynamic filterings, one would call the `filter()`-method on the
report, passing the type of element to be filtered, and to which element IDs to
reduce the report data to. The following reduces the report to only return the
rows with the `product01` Product and `location02` Location elements:

```java
new Report(tx, "stockReport")
        .filter("Product", "product01")
        .filter("Location", "location02")
        .doReportAsJson()
```

It is possible to find the possible filter criteria dynamically using the
generateFilterCriteria() method.

### Date Range Filtering

The last option to filter dynamically is using a date range selector. Define the
dateRangeSel lookup parameter, and then set the date range on the instantiated
report:

Model the report in XML:
```xml
<ParameterBag Id="parameters" Name="parameters" Type="Parameters">
    ...
    <Parameter Id="dateRangeSel" Name="Date Range Selector" Type="String" Interpretation="Resource-Ref" Uom="Product" Value="Bags/parameters/expirationDate"/>
    ...
</ParameterBag>
```

And now call the report in Java:
```java
Date from = new Date(LocalDate.of(2016, 1, 1).toEpochDay() * 86400000);
Date to = new Date(LocalDate.of(2017, 1, 1).toEpochDay() * 86400000);
DateRange dateRange = new DateRange().from(from, true).to(to, false);
List<JsonObject> result = new Report(tx, "stockReport") //
    .filter("Product", "product01") //
    .dateRange(dateRange) //
    .doReportAsJson()
```

{{% notice tip %}}
Note: See the [GenericReportTest](https://github.com/strolch-li/strolch/blob/develop/li.strolch.service/src/test/java/li/strolch/report/GenericReportTest.java) for examples.
{{% /notice %}}

---
title: 'Strolch Utils'
weight: 230
---

## Strolch Utils

The `strolch-utils` module is a collection of project-independent Java utility classes and helpers designed to simplify common programming tasks.

### Key Components

#### Design by Contract (DBC)
The `DBC` (Design by Contract) enum provides a set of assertion methods to enforce preconditions, postconditions, and intermediate checks.

```java
public void setAge(int age) {
    DBC.PRE.assertTrue("Age must be positive", age > 0);
    this.age = age;
}
```

#### ObjectFilter
The `ObjectFilter` is a utility for tracking modifications (add, update, remove) to a set of objects. It ensures that only the necessary operations are performed at the end of a process.

#### ISO8601 Date/Time
Strolch uses ISO 8601 for date and time representation. The `li.strolch.utils.iso8601` package provides helpers for parsing and formatting dates consistently.

#### Specialized Collections
The `li.strolch.utils.collections` package contains several specialized classes:
- `MapOfLists`: A map where each key maps to a list of values.
- `MapOfSets`: A map where each key maps to a set of values.
- `MapOfMaps`: A nested map structure.
- `Paging`: A utility for handling paginated data sets.

#### Helper Classes
A wide range of helper classes are available in `li.strolch.utils.helper`:
- `StringHelper`: String manipulation and conversion.
- `FileHelper`: File and directory operations.
- `XmlHelper`: XML parsing and formatting.
- `DateHelper`: Date manipulation and calculations.
- `ExceptionHelper`: Utilities for handling exceptions.
- `SystemHelper`: Access to system-specific information.

#### Time Utilities
The `li.strolch.utils.time` package provides advanced time manipulation utilities, including `PeriodHelper`, `Interval`, and `PeriodDuration`.

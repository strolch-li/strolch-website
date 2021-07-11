---
title: 'Do and Don''t'
weight: 30
---

## This page discusses things you should and shouldn't do when using Strolch
The following is a simple list of do's and don'ts:
* 1 `Service` per use-case, should mostly delegate to `Commands`.
* `Commands` implement use-cases or parts of it, and are thus reusable.
* Subclass `ResourceSearch`, `OrderSearch` and `ActivitySearch` when implementing use-case specific search - this allows privilege checking.
* One Transaction at a time - no TX inside of another TX.
* Commands are added to TXs and performed on close: `tx.addCommand(Command)` and then `tx.commitOnClose()`
* Use `tx.flush()` if you need to perform first some work and then as late as possible call `tx.commitOnClose()`
* Only access `ElementMaps` if really no other way, mostly use `tx.get*By()`, `tx.findBy()` and searches - if a specific get is missing, then add the method to `StrolchTransaction` and send a pull request!
* Use `tx.stream*()` methods to iterate over all elements, if you don't want to use a search.
* Don't write logic in REST API beans. Delegate to other services, making your code reusable!
* Transform to JSON using the `StrolchElementToJsonVisitor`.
* References between objects is done by adding a `ParameterBag` with the id `relations` to the object and then `StringParameters` with the value being the ID, the UOM set to the type of element being referenced and the Interpretation set to the class type being referenced.

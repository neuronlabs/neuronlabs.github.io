---
parent: Query
grand_parent: Core ORM
nav_order: 10
title: Sorts
---

![Logo](/assets/img/logo.svg)

# Sorts

Neuron allows to sort the `List` method results. The sorting is based on the provided field's value. It allows both `ascending` and `desceding` ordering.

```
NOTE: Current implementation allows sorting by the fields of the query scope's root model only.
```

## Scope Sorting

By default scope sorts by the primary field values in ascending order.

In order to sort the results by other fields, use the scope's `Sort` method.

The function takes multiple string 'fields' as an arguments. By default each sort field would be in `ascending` order. In order to change it into `descending` order the `-` sign should be used before given 'field'.

Example:

```go
// Sort the scope's result by the name, and if that matches sort by the age in 
// desceding order.
err = scope.Sort("name", "-age")
```

This would result in sorting the results by the `name`. In case multiple fields would match, these would be sorted by the `age` in desceding order.
---
parent: Query
grand_parent: Core ORM
nav_order: 3
title: Get
---

![Logo](/assets/img/logo.svg)

# Getting Single Data

The neuron specifies separate function for getting just a single data instance from the repository.

In order to get the single data we need to provide a model pointer to the 
new query scope methods:

```go
var u User

// create new query for the 
s := norm.MustNew(&u)

// s.Collection() == 'users'
```

## Filters

In order to get a single data we should provide a filter.

```go
// add the filter to the scope for user with id=4
err = s.AddStringFilter("filter[users][id][$eq]", 4)
```

## Fieldset 

By providing the fieldset we can define what fields we would like to get 
from the repository. 
<!--
TODO: Fieldset information about primary field key.
The primary field key would always be taken from the repository.
-->

```go
err = s.SetFieldset("id", "name", "age", "pets")
```

## Pagination

If provided filters doesn't specify the primary field key, the scope may contain also a pagination offset:

```go
limit, offset := 1, 10

// set the limit and offset
err = s.Limit(limit, offset)
```
If the limit is greater than 1 the results would still contain a single instance in `Get` method. 

## Methods

When the query is prepared use the `Get` or `GetContext` methods for the query
to get a single data instance from the repository.

```go
err = s.GetContext(ctx)
if err != nil {
    // handle the error
}
```

## Relations

If the fieldset contains any relationship field, the query processor would take it's primary field key and store in the relationship field's primary.

```go
fmt.Println(u.Pets)
{% assign pets = '[]*Pets{{ID: 5}, {ID: 10}}' %}// {{ pets }}
```




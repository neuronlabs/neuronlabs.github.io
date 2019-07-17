---
parent: Query
grand_parent: Core ORM
nav_order: 3
title: List
---
# List method

Listing the data is one of the most important ORM functions.

Neuron allows to query along multiple repositories joining all the relations for each single data instance. 


## Code

Creating a List Query requires to provide an address of slice of models, to the `query.New`, `query.NewC`, `query.MustNew`, `query.MustNewC` functions:

```go
users := []*User{}

s, err := query.New(&users)
```

Having prepared the query scope, we can specify what data we would like to obtain.

```go
// add the field filter
if err = s.AddStringFilter("filter[users][id][$ne]=51"); err != nil {
    return err
}

limit := 5
offset := 12

// limit the values number at specific offset
if err = s.Limit(limit, offset); err != nil {
    return err
}


// Set the return field set
if err = s.SetFieldset("name", "pets"); err != nil {
    return err
}


// sort by the primary field in a decreasing order
if err = s.SortBy("-id"); err != nil {
    return err
}
```

Such prepared query Scope is ready for basic query preparation.
The `List` or `ListContext` methods would do the list process. 

```go
// if we have a context we need to include into consider use ListContext method
// otherwise choose List.
if err = s.List(); err != nil {
    return err
}

// now the scope value - variable 'users' should contain
// the required values.
```

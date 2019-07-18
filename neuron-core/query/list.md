---
parent: Query
grand_parent: Core ORM
nav_order: 4
title: List
---
# Listing data

Listing the data is one of the most important ORM functions.

Neuron allows to query along multiple repositories joining efficiently all the relations.

By default while getting the relation data, neuron gets only relationship's primary id's. 

<!-- 
TODO: Add information about including the scope data.
-->


## Prepare query scope

Creating a List Query requires to provide an address of slice of models, to the `query.New`, `query.NewC`, `query.MustNew`, `query.MustNewC` functions:

```go
users := []*User{}

s, err := query.New(&users)
```

Having prepared the query scope, we can specify what data we would like to obtain.

```go
// add the field filter
if err = s.AddStringFilter("filter[users][id][$ne]", 51); err != nil {
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

Such prepared query Scope is ready for processing.
The `List` or `ListContext` are the methods reponsible to list all the specified data instance from the repositories.

```go
// if we have a context we need to include into consider use ListContext method
// otherwise choose List.
if err = s.List(); err != nil {
    return err
}

// now the scope value - variable 'users' should contain
// the required values.
```

As a result the arguments provided while creating the query scope would contain the data instances taken from repositories.

If no data would be find for given query an error of class `class.QueryValueNoResult` would be returned.

```go
// use previously prepared query scope to list the data values
err = s.List()
if  err != nil && !errors.IsNoResult(err) {
    return err
}
```
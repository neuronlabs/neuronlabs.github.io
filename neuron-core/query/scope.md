---
parent: Query
grand_parent: Core ORM
nav_order: 1
title: Scope
---

![Logo](/assets/img/logo.svg)

# Scope

Scope is the data structure that contains all the information about given query. It keeps the information about the filtering, sorting, pagination, fieldset, selected fields, value and many other meta data. Each of it is accessible from the `*query.Scope` instance as the methods or variables: 

* ID - unique query **id** number (UUID)
* Value - Query's value defines the values to be returned (*GET* and *LIST*), created (*CREATE*), changed (*PATCH*) or deleted (*DELETE*). 
* [Filters](filters.html) - query filters divided by the field types
    - Primary Key filters
    - Foreign Keys filters
    - Attribute filters
    - Relationship filters
    - Filter Keys filters
* [Fieldset](fieldset.html) - set of fields that must be returned with their values from the repository
* [Selected Fields](selected_fields.html) - set of fields selected to create/change any repository model
* [Sorting Order](sorts.html) - sorting order of the query
* [Pagination](pagination.html) - query paginations

## How to create a query Scope?

A query scope can be created from the root `github.com/neuronlabs/neuron-core`  or the `github.com/neuronlabs/neuron-core/query` package.

In order to create a new Scope, it's valid root model must be provided as well as the controller that keeps related models.



### Create with value

A scope might be create with model value using `ncore.Query`, `ncore.QueryC`, `ncore.MustQuery` or `ncore.MustQueryC`. The functions with the 'C' ending requires the controller to be provided as the argument, whereas the other use default controller.

Example Must:

```go
m := &Model{ID: 3}

// This function creates new scope with the default controller and 
// the model 'm'. It panic on error.
scope := ncore.MustQuery(m)
```

Example QueryC:

```go
// supposed that we have already a controller 'c' with registered 'Model'.
m := &Model{ID: 2, Name: "Anthony"}

// This takes the 'c' controller and the model 'm'.
// Returns error if the model's value is not valid.
scope, err := ncore.QueryC(c, m)
```

These queries might be also created using direct access to the `github.com/neuronlabs/neuron-core/query` with methods such as: `query.New`, `query.NewC`, `query.MustNew`, `query.MustNewC`. The functions with 'C' endings requires the `controller.Controller` to be provided as argument, whereas the others would use the Default Controller.

### Scope allowed Values

The scope keeps two types of values:

* single instance - it is a non-nil pointer to the value model.
* multiple instances - a non-nil pointer to the slice of pointers to models.

Examples: 

```go

single := Model{ID: 5}
{% assign manyValue = '[]*Model{{ID: 3}}' %}
many := {{ manyValue }}
// This would be correctly created scope - the value taken is an 
// address of model/slice instances.
scope := query.MustNew(&single)
scope2 := query.MustNew(&many)

// This two would panic, the value taken are not pointers to the structures.
scope = query.MustNew(single)
scope = query.MustNew(many)
```

### Create scope with *ModelStruct

The query might be also created using a `*ModelStruct` with the package `github.com/neuronlabs/neuron-core/query`. The method `query.NewModelC` is used to create scope with the ModelStructure. It requires controller to be provided as an argument, as well as information if the value of the query should be a slice or a single value instance `isMany` boolean. Created scope's values would depend on the `isMany` flag, and their types would looks like: 
{% assign many = '*[]*Type{}' %}
* isMany - true - `{{ many }}`
* isMany - false - `*Type{}`

Where the `Type` is the model registered in the controller.

Example:

```go
// supposed that the controller 'c' was previously 
// created with registered model 'Model'.

mStruct, _ := c.ModelStruct(Model{})
scope := query.NewModelC(c, mStruct, true)

var many []*Model
// scope's Value is a non-nil pointer of slice of pointer models
temp := scope.Value.(*[]*Model)

// dereference the pointer to get the slice of models.
many = *temp

```

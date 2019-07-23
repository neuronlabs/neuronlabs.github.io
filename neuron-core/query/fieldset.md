---
parent: Query
grand_parent: Core ORM
nav_order: 8
title: Fieldset
---
# Fieldset

Query `fieldset` defines the fields that would be returned with the query resutls. It is used on results of `Get` and `List` methods.

If no fields in fieldset were specified for the query, the processor would add 
all the possible model's fields.

While listing multiple values or querying over the relationships it might significantly increase the query speed.

### JSONAPI

The package `encoding/jsonapi` uses fieldset in `MarshalScope` functions. It defines which attributes and relationships should be marshaled into output data. 

## Fieldset Methods

The `*query.Scope` provide following methods for the access to the fieldset:

### SetFields

Adds 'fields' to the fieldset of the scope. Each field might be a string struct field **Name**,  **Neuron Name** or a __*mapping.StructField__.
Returns error if the field is not found within the query scope root model.
```go
err := scope.SetFields("Name", "custom_neuron_name")
```

### List Fieldset

`Fieldset` method returns all the fields in the scope's Fieldset.
The returned fields are: __[]*mapping.StructField__.

```go
var fieldset []*mapping.StructField

fieldset = scope.Fieldset()
```

### In Fieldset

The `InFieldset` method checks if provided field is in the scope's Fieldset.
The 'field' might be a StructField **Name** or a **Neuron Name**.

```go
isInFieldset, err := scope.InFieldset("Name")
```

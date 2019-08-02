---
parent: Query
grand_parent: Core ORM
nav_order: 9
title: Selected Fields
---

![Logo](/assets/img/logo.svg)

# Selected Fields

For the queries where the provided input model would have usable values, the selected field defines are the fields that should be used by the processor.

By default - (if no fields were selected for the query) all **non-zero** valued fields would be added to the query's `Selected Fields`.

If the zero-valued field should be used, it needs to be added to the query's `Selected Fields`.

### JSONAPI

It is also used by the `encoding/jsonapi` package. While unmarshaling the scope with specified values it selects only unmarshaled fields from the input data.



## Selected Fields methods

The query allows to do multiple operations on the given scope.
It allows to check what fields are selected, return all non selected fields, select single or multiple fields.

Prepare the query scope:
```go
// User is the neuron model used for the purpose of this documentation.
type User struct {
    ID          int
    Name        string `neuron:"name=custom_name"`
    Age         int
    PhoneNumber string
}

// create model instance
user := User{ID: 1, Name: "Miguel", Age: 17}

// 
scope := MustNew(&user)

```

### IsSelected

is selected checks if the provided argument 'field' is selected within the query's scope.

The `field` might be a string struct field **Name**,  **Neuron Name** or a __*mapping.StructField__.
```go
// In order to check if the field is selected the function allows us to use multiple 'field' types:

// 1. StructField Name
isNameSelected, err := scope.IsSelected("Name")

// 2. Neuron Name
isNameSelected, err = scope.IsSelected("custom_name")

// 3. *mapping.StructField
field, _ := s.Struct().FieldByName("Name")
isNameSelected, err = scope.IsSelected(field)
```

### SelectField

Selects the field in the scope's query. The 'field' must be a string structfield's **Name** or  **Neuron Name**.

```go
// the
err := scope.SelectField("Age")

err = scope.SelectField("custom_names")
```

### SelectFields 

Clears the selected fields container for given query.Scope and adds argumented 'fields'. Each field might be a string struct field **Name**,  **Neuron Name** or a __*mapping.StructField__.

```go
// Each field may be of different type
phoneNumber, _ := scope.Struct().FieldByName("PhoneNumber")
err = scope.SelectFields("Age", "custom_names", phoneNumber)
```

### AppendSelectedFields

Appends provided fields to the selected fields container for given query.Scope.
Each field might be a string struct field **Name**,  **Neuron Name** or a __*mapping.StructField__.

```go
// Each field may be of different type
phoneNumber, _ := scope.Struct().FieldByName("PhoneNumber")
err = scope.AppendSelectedFields("Age", "custom_names", phoneNumber)
```


### SelectedFields

Lists all the selected fields in the query's scope. Returns __[]*mapping.StructField__.

```go 

var selectedFields []*mapping.StructField

selectedFields = scope.SelectedFields()
```

### NotSelectedFields

Lists all the fields that were not selected in the query's scope.
Returns __[]*mapping.StructField__.

```go
var notSelectedFields []*mapping.StructField

notSelectedFields = scope.NotSelectedFields()
```
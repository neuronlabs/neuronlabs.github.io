---
parent: Query
grand_parent: Core ORM
nav_order: 5
title: Patch
---

![Logo](/assets/img/logo.svg)

# Patching single data

The neuron allows to patch provided data instances in the repositories.
It changes selected fields for the provided data model instance.

Firstly the query scope needs to be created:

```go
// prepare the model data instance
u := User{
    // Primary key
    ID: 5,
    // Attribute
    Name: "Anthony",
    // Relationship
    Pets: {% assign pets_value = '[]*Pet{{ID: 5},{ID: 12}}' %}{{ pets_value }},
}

// create new query scope for the 'u' User.
s := norm.MustNew(&u)
```

## Fitler

In order to patch, the query processor requires that the query scope must have any kind of filter.

While providing the data with **non zero primary key field**, neuron processor would take it's value and set as the primary key filter with `$eq` filter operator.



## Validation

Each patching data might be validated before the operation starts.
The validation is done by the [gopkg.in/go-playground/validator.v9](https://gopkg.in/go-playground/validator.v9) validator.

The default patch validator might be changed by setting controller's `PatchValidator`.


## Selected Fields

It is worth to emphasize that the **Patch** updates **only** the Selected fields.

If no `selected fields` are provided for the query scope, the query processor would take all the non-zero values of the provided model.

If we want to change the field's value to it's zero value i.e.:

```go
// Part is the example model containing zero-value attribute fields.
type Part struct {
    ID      int 
    Price   int
    Amount  int
}

// set the model with it's primary key equal to '1'
p := Part{ID: 1}

// we want to patch the amount to '0'
s := query.MustNew(&p)

// in order to use the 'Amount' field it must be marked as selected.
err = s.SelectField("Amount")
if err != nil {
    // error might occur on misspeling the field name
}
```

## Patching Methods

When the query scope is ready for patching, use the query scope's methods:
`Patch` or `PatchContext` if the query should be based on the `context.Context`.

```go
err = s.Patch()
if err != nil {
    // handle the patch error
}
```

## Relations 

When the query scope has selected the relationship field the patch would change their values depending on the relationship type and field's value.

```
In future versions of the Core ORM each relationship would have it's change strategy. The strategy would differ the processes on data patching, depending on the user's choice.
```

### BelongsTo relationship

In `BelongsTo` relationship the root model contains related `foreign key` for the relationship. 
If the selected relationship field would be a non nil, the processor copies it's **related** primary field key into related foreign key. If it's nil it would set it's **foreign key** to zero value.

### HasOne relationship

For models with selected `HasOne` relationship field, if it's value is:

*  non-zero model instance - then the processor would patch the related model's foreign key.
*  nil value - the processor clears the related model's foreign key by setting it to zero-value.

### HasMany relationship

For models with selected `HasMany` relationship field, if it's value is:

* non-zero multi model instances - the processor clears old related models foreign keys and sets current related models **foreign key** field to the **root** model **primary key** field value.
* empty slice - the processor clears previous related model's **foreign keys** that were equal to the **root** model's **primary key** field value.

### ManyToMany relationship

For models with selected `Many2Many` relationship field, if it's value is:

* non-zero multi model instances - the processor clears old related models foreign keys in the **join model** and sets current related models **foreign key** field in the **join model** to the **root** model **primary key** field value.
* empty slice - the processor clears previous **join model's** data instances where the **foreign key** is equal to the model's **primary key field** value.

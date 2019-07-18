---
parent: Query
grand_parent: Core ORM
nav_order: 6
title: Delete
---
# Deleting data

Deleting the data instances is based on the query scope filters.

Create new query scope for the model that would be deleted:

```go
// we want to delete the users collection's instance with id: '4'
u := User{ID: 4}

// prepare the query scope
s := norm.MustNew(&u)
```

## Filters

In order to delete the data model's instance we need to provide query scope filters. 

If the model provided in the query creation method contains non-zero primary field value, the neuron **processor** sets it's value to scope's primary filter.

## Deleting Methods

When the query scope is ready for deleting, use the query Scope's methods:
`Delete` or `DeleteContext` if the query should be based on the 'context.Context'.


```go
err = s.Delete()
if  err != nil 
    // a different logic may be provided for specified error.
    // If no values were deleted the processor returns errors with class
    // QueryValueNoResult.
    if  !errors.IsNoResult(err) {    
        return err
    }
}
```

## Relations

While deleting current data instance the processor clears also related relationship foreign keys. 

```
In future versions of the Core ORM each relationship would have it's change strategy. The strategy would differ the processes on data deletion, depending on the user's choice.
```

### BelongsTo

In `BelongsTo` relationship the root model contains a **foreign** key which would be deleted with the model indstance.


### HasOne

The models with `HasOne` relationships would have related model's foreign keys deleted.

### HasMany

The models with `HasMany` relationships would have related model's foreign keys deleted.

### ManyToMany

The models with `Many2Many` relationships would have related model's foreign keys deleted from the **join model**. 
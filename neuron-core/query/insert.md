---
parent: Query
grand_parent: Core ORM
nav_order: 2
title: Insert
---

![Logo](/assets/img/logo.svg)

# Insert data

In order to insert the data create a new query with the provided value:

```go
user := &User{Name: "Jack"}

s, err := query.New(user)
if err != nil {
    return err
}
```

Any create query may have specified it's Selected Fields. The selected fields are the field's which values would be inserted into repository. By default the query processor scans provided `scope.Value` for non-zero field values and uses them as the selected fields. By specifing selected fields the query might insert zero-valued fields.

```go
err = s.SelectFields("Name", "Age")
if err != nil {
    return err
}
```

When the create query preparation is finished then the `Create` method should be used on the `query.Scope`.

```go
err = s.Create()
```

**OR** with respect  to the provided `context.Context`:

```go
ctx, cancel := context.WithTimeout(context.Background, time.Second * 5)
defer cancel()

err = s.CreateContext(ctx)
```

If the query.Scope doesn't have primary field selected then the repository **should** create new model instance and set it's ID value into model's primary field value.

```go
id := user.ID // newly created model's ID
```

## Relations

While inserting the model, `neuron` allows to set the relations for given model within a single query. If the relationship field **primary** field(s) is non-zero then after having the root model value created, the following relationship field would have it's relationship model's patched with the selected foreign keys.

```go 
user := &User{Name: "Thomas", Pets: []*Pet{ID: 5}}

s, err = query.New(user)
if err != nil {
    return err
}

// the Create query would create new user with name Thomas
// and patch pets model's with id: '5' so that it's foreign key is set
// to newly created user's id.
err = s.Create()
if err != nil {
    return err
}
```

### Errors

While patching the related values an error might occur. 
If the query was within a single transaction, then 

// possible strategies:
- transaction:
    + rollback all changes of the query
- non transactions:
    + return error - this should be done within a transaction - any change might provide 
    + continue WithTimeout error

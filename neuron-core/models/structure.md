---
parent: Models
grand_parent: Core ORM
nav_order: 1
title: Structure
---

# Model Structure

The `*ModelStruct` is a data structure that stores all prepared information about the 'model'.

This document would base it's examples on the provided models:

```go
// Bird is the neuron model used for the documentation.
// It's fields have cardcoded neuron types.
type Bird struct {
    ID   int    `neuron:"type=primary"`
    Name string `neuron:"type=attr"`
    Age  int    `neuron:"type=attr"`

    Specie       *BirdSpecie `neuron:"type=relation"`
    BirdSpecieID int       `neuron:"type=foreign"`
}

// BirdSpecie is the neuron model used for the documentation.
// It defines the birds species.
type BirdSpecie struct {
    ID      int
    Name    string
}
```
A model stores following variables:

* Type - specified `reflect.Type` that is dereferenced model type reflection. 
* Collection - pluralized name converted using `Controller's` NamerFunc.
* Config - is the `*config.Model` matched for given model.
* Store - is the *key*:*value* storage for given model.
* Primary Key Field - is the [*mapping.StructField](#structfield) of the model's primary index field.
* Attribute Fields - _(optional)_ model may contain multiple [*mapping.StructField](#structfield) attributes. An attribute is the non related information about the model stored directly in the model's repository.
* Relationship Fields - _(optional)_ relationship fields represents references to the other models stored as struct fields.
* ForeignKey Fields - _(optional)_ foreign keys are the fields directly connected with the relationship. It defines matched **primary key** of the related field's model. 
* Language Field - _(optional)_ is a singleton (per model), optional field that defines `lanaguage` and `i18n` related values.
* Filter Key Fields - _(optional)_ filter key fields are the fields which values are not stored within the model's repository. Their value should be used to specify some state that might be usable in the Hooks. 

## Registering Models

Neuron requires to register the model before any operation starts.
Registering model, stores it's `*mapping.ModelStruct`  in the `Controller`.
A controller can have only a single model related to each `reflect.Type` and a single `collection`. 

Register the models with the `RegisterModels` `Controller's` method.
It takes the model value instances as arguments.
If a model have relationship fields, all the related models must be registered at once. The order of the provided models doesn't matter.

```go
err := c.RegisterModels(Bird{}, BirdSpecie{})
if err != nil {
    // the model definition is invalid.
    return err
}
```


### Getting Model Struct

Having our models registered, we might want to use it's mapping structure.
This would be done by the `Controller's` `ModelStruct` method:

```go
modelStruct, err := c.ModelStruct(Bird{})
if err != nil {
    // the model is not found within given controller.
    return err
}
```

---
nav_order: 3
parent: Core ORM
title: Models
---

# Models

Neuron used struct types with specific fields as the models. 

The model structure were based on the [JSON:API v1.0](https://jsonapi.org) specification. It's design distinguished fields by their kinds. This feature made it easier to isolate the model's by their own repositories.

Neuron maps each model, analyze it's fields with **tags** and creates `*mapping.ModelStruct`. These models are registered in the `Controller`.

## Model Structure

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

### Config

While registering the models, each ModelConfig defined under model's `collection` in the `Controller's` ` Config` would be set in the `ModelStruct` definition. 

It's `RepositoryName` value defines to which repository should the model be connected. 

The `Store` field is being copied to the model's store values. This allows to specify repository related variables.

The `AutoMigrate` flag is the information to the repository if the model should be migrated within the repository (database tables etc).

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

## StructField

A `*mapping.StructField` is the mapped model's field. It contains neuron information about given field, such as:

* ModelStruct - the model where the field is defined
* Neuron Name - is the field's name converted using controller's defined `NamerFunc`.
* Name - the structField's Name.
* FieldKind - is the neuron kind of the field: 
    - Primary Key
    - Foreign Key
    - Attribute
    - RelationshipSingle
    - RelationshipMultiple
    - Filter Key
    - Nested - for the nested attributes
* Nested - nested structure for the nested field types.
* [Relationship](#relationship) - relationship contains information about the field's relation to another model.
* ReflectField - is the `reflect.StructField` reflection
* Store - is the _key_:_value_ storage for given field.

## Field Tags

In order to specify field's specifictaion each field may be tagged with some specific **tag**. The basic struct tag is named: `neuron`. Under this tag there might be multiple **tags** seperated with `;` sign. Each tag may take additional _key_=_value_ seperated with `,`.
Fields tagged with `-` would not be mapped into neuron structures.

Example: 

```go
type TestingModel struct {
    ID    string `neuron:"type=primary"`
    Name  string `neuron:"type=attr;flags=omitempty,nosort"`

    NonNeuronField int `neuron:"-"`
}

// ID field has a neuron tags: `type` with value `primary`
// Name field has tags:
//  - 'type' with value 'attr' - Attribute field
//  - 'flags' with values seperated by ',' - 'omitempty' and 'nosort'.
// NonNeuronField has tag: '-' which defines that 
// this is not a neuron field, omit and don't map it.
```

Tags: 

|   Tag     | Description | Requirements |
|----------:|-------------|:------------:|
|   name    | Define the name of the field | |
|   type    | Define the [field type](#field_type_tags) | |
|   foreign | Define the name('s) for the foreign key('s) for given relationship| Relationship|
| many2many | Defined given field as Many2Many relationship. If the values are provided, the first would be a foreign key and the second - related model's foreign key.| ToMany Relationship| 
|   flags   | Struct field tag that allows to store the field's [flags](#flags) | - | 
|     -     | Omits given field in mapping process | |


### Flags 

Field's flags changes the field's definitions. They're defined in the struct field's tag under `neuron->flags` tag.
It is also allowed to specify **flags** within the Field's Config store. 

| Flag    | Description | Requirements|
|--------:|-------------|:-----------:|
| client-id | Defines if the provided field allows to create new model instances with user defined ID values | Primary Key Field |
| nofilter | Disallow to filter the model by given field| |
| nosort   | Disallow to sort the model by given field | |
| omitempty | encoding/jsonapi would omit this field if it's value is empty | |
| hidden | encoding/jsonapi would not marshal this field | 
| lang | given field is a language type field | Attribute, Singleton in given Model |
| iso8601 | Is the marshaling and unmarshaling information about the format of given time field | Time field |

### Field Types Tags

Allowed field type tags:

|   Tag   | Description |
|--------:|-------------|
| primary, primary_key, primarykey, id, pk | primary key field definitions |
| attr, attribute | Attribute fields type |
| relation, relationship | Relationship field types |
| foreign, foreign_key, foreignkey, fk | Foreign key type definiton |
| filterkey | Filter Key type definition |


## Relationship

Relationship is the structure that contains information about the relationship field for given model.
It defines information such that: 

* Kind - defines the relationship kind
* ForeignKey - is the struct field that defines relationship's foreign key
* Join Model - (optional) used only for the ManyToMany models as the join model definition.
* ManyToManyForeignkey - (optional) is the foreign key used on the ManyToMany relationship that defines related model's foreign key within the `JoinModel`
* Struct - defines the related model structure.
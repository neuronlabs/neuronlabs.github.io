---
parent: Models
grand_parent: Core ORM
nav_order: 2
title: Fields
---

# StructField

A `*mapping.StructField` is the mapped model's field. It contains neuron information about given field, such as:

* ModelStruct - the model where the field is defined
* Neuron Name - is the field's name converted using controller's defined `NamerFunc`.
* Name - the structField's Name.
* FieldKind - is the neuron kind of the field: 
    - Primary Key - the primary key field of the model
    - Foreign Key - the foreign key field for the model's relationship
    - Attribute - an attribute of the model
    - RelationshipSingle - single value relationship field
    - RelationshipMultiple - multiple value relationship field
    - Filter Key - a key value used to change the hooks
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
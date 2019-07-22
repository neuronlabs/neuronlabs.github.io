---
parent: Models
grand_parent: Core ORM
nav_order: 3
title: Relationships
---

# Relationship

Relationship is the structure that contains information about the relationship field for given model.
It defines information such that: 

* Kind - defines the relationship kind
* ForeignKey - is the struct field that defines relationship's foreign key
* Join Model - (optional) used only for the ManyToMany models as the join model definition.
* ManyToManyForeignkey - (optional) is the foreign key used on the ManyToMany relationship that defines related model's foreign key within the `JoinModel`
* Struct - defines the related model structure.

<!--
TODO: Add relationship types documentation.
-->

## Relationship kinds

The relationship type might differ a lot in their structure and value number. Neuron specifies 4 main relationship kinds:
- [Belongs to](#belongs-to)
- [Has one](#has-one)
- [Has many](#has-many)
- [Many to many](#many-to-many)

### Belongs to

The `Belongs to` relationship defines the single value relationship where it's `Foreign Key` is stored within the root model. 

Example:

Let's define the root model with the belong's to relationship:
```go
type User struct {
    ID          int
    Name        string
    FavoritePet *Pet
    PetID       int
}
```

And the related model:
```go
type Pet struct {
    ID   int
    Name string
}
```

The example `User` model contains `BelongsTo` relationship field with name `FavoritePet` and foreign key: `PetID`. 

#### Foreign key naming convention
The default relationship foreign key naming conventions specify that the foreign key name should be:
- relationship's struct field name + ID - example: `FavoritePetID`
- relationship's struct name + ID - example: `PetID`


If the other name is required for the model naming use the `foreign` tag with specified name on the relationship that uses this foreign key field.

If the model or related model contains multiple fields with the matched foreig n key name the mapper prioritizes them as follows:

* Custom foreign key name (`CustomForeignID`)
* Relationships struct field name (`FavoritePetID`)
* Relationships struct name (`PetID`)


### Has one 

The `HasOne` relationship defines the single value relationship where the **related model** contains the `Foreign Key`. 

This relationship searches for the foreign key named from the model that contains relationship field instead of the foreign model (as in the `BelongsTo` relationship).

Example:

Define the model with `HasOne` relationship field:

```go
type Pet struct {
    ID    int
    Name  string
    Owner *User
}
```

Define the related model with the foreign key - this is the name of the related model with ID (as in the `BelongsTo` relationship) :

```go
type User struct {
    ID          int
    Name        string
    FavoritePet *Pet
    PetID       int
}
```

The example `Pet` model contains the `HasOne` relationship field `Owner` which has it's foreign key in the `User` model under the name `PetID`.

### Has Many

The `HasMany` defines the `many to one` relationship between the models where the `Foreign Keys` are stored in the **related models**. 

This relationship searches for the foreign key named from the model that contains relationship field instead of the foreign model (as in the `BelongsTo` relationship).

Example:

Define the model with foreign key:

```go
type Human struct {
    ID      int
    Name    string
    Surname string
    // HouseID is the foreign key of the House.Owners has many relationship
    HouseID int
}
```

Define the model with the `HasMany` relationship field:

```go
type House struct {
    ID      int
    Address string
    // Owners is the has many relationship field with the foreign key
    // stored in the Human model (HouseID)
    Owners  []*Human 
}
```

The example above defines two models: 
- Human which contains ID, some attributes and a `HouseID` foreign key.
- House which contains ID, address attribute and the `Owners` `HasMany` relationship. The controller searches for the foreign key of names: `OwnerID` or `HouseID`. The second is found within the `Human` model.

This relation can be paired with the opposite BelongsTo relationship, where the example `House` model has some `BelongsTo` relaitonship field to the `House`.

### Many to many

The `ManyToMany` model is the most advance relationship type. It requires three models to involved:

* Model with relationship
* Related model
* Join model containing foreign keys

By default the join model's name should be a concantenation of the relationship models where the second is in plural form. In order to change it's default name set the subtag with its name under the `many2many` tag.

Example:

Define model with relationship:

```go
type Class struct {
    ID          int
    Title       string
    Students    []*Student `neuron:"many2many"`
}
```

The root model has the `Students` relationship field. It must be tagged with the `many2many` struct field tag.

Define the related model:

```go
type Student struct {
    ID      int 
    Name    string
    // Classes is the many2many relationship field
    // As the backreference field, it is not required to exists.
    Classes []*Class `neuron:"many2many"`
}
```

The backreference field in the related model is not required. If it exists it must be tagged with the `many2many` struct field tag.

Define the join model:

```go
type ClassStudents struct {
    ID          int 
    StudentID   int 
    ClassID     int
}
```

The join model may have attributes and other relationship fields (i.e.:`BelongsTo` relationships to the `Class` or `Student`)

The above example shows how the three models could be defined for the `ManyToMany` relationship. 

The `foreign` struct field tag in the `ManyToMany` relationship may accept a subtag that defines custom join model name. In order to customize the foreign key names use `foreign` tag with two subtags as follows:

The root model has the `Students` relationship field. It must be tagged with the `foreign`, and it's custom foreign key name should be stored as the subtag.

```go
type Class struct {
    ID          int
    Title       string
    // Students many2many relationship field has both custom join model
    // and custom foreign key name.
    Students    []*Student `neuron:"many2many=CustomJoin;foreign=CustomClassID"`
}
```


The backreference field in the related model is not required. If the related model's foreign key have custom name it must be shown as the second subtag name in the `foreign` tag. If no custom foreign key name is used for this model but it is used for the related model use the `_` subtag to separate the names.
Define the related model:

```go
type Student struct {
    ID      int 
    Name    string
    // Classes many2many relationship field has both custom join model and 
    // custom related models foreign key name.
    Classes []*Class `neuron:"many2many=CustomJoin;foreign=_,CustomClassID"`
}
```


This join model has custom name different than default convention. It must be set in the `many2many` tag of the `ManyToMany` relationship fields.

```go
type CustomJoin struct {
    ID              int
    // StudentID is the foreign key with default name. 
    StudentID       int
    // CustomClassID is the foreign key with custom - non default - name.
    CustomClassID   int 
}
```




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

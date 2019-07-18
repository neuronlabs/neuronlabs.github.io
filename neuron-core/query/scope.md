---
parent: Query
grand_parent: Core ORM
nav_order: 1
---

# Scope

Neuron-core's architecture allows to query models with their relations located on separate databases, stores or services. This forces the query to be very precise in it's structure.

Each query is stored as the `query.Scope` which consists of the following components:

* [ID](#id) - unique query **id** number (UUID)
* [Value](#value) - query specific value/values
* [Filters](filters.html) - query filters divided by the field types
    - Primary Key filters
    - Foreign Keys filters
    - Attribute filters
    - Relationship filters
    - Filter Keys filters
* [Fieldset](fieldset.html) - set of fields that must be returned with their values from the repository
* [Selected Fields](selected_fields.html) - set of fields selected to create/change any repository model
* [Sorting Order](sorts.html) - sorting order of the query
* [Pagination](pagination.html) - query paginations

## ID 

Each query scope contains uniquely set ID stored as UUID.

## Value

Query's value defines the values to be returned (*GET* and *LIST*), created (*CREATE*), changed (*PATCH*) or deleted (*DELETE*). 





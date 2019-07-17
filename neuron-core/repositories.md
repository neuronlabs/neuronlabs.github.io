---
nav_order: 2
parent: Core ORM
---
# Repositories

Neuron-core use the repositories as the database/data store/service access.
The main abstractions of the repositories are [repository.Factory](#factory) and a [repository.Repository](#repository).

## Factory
The Factory is the abstraction used to create new repository instances.
It is also responsible for keeping the instances and closing them.
Each factory have a **unique** driver name, which is used by the repositories  configurations (`*config.Repository`) and by the `*controller.Controller` which is responsible to get the appropiate factory and create new repository instances for the models. The Factory should be registered within the `neuron-core` on package initialization.

Custom factory skeleton may look like in the following example:
```go
package myfactory

import (
    "context"

    "github.com/neuronlabs/neuron-core/mapping"
    "github.com/neuronlabs/neuron-core/repository"
)

// this factory should be registered on the blank import.
func init(){
    // this factory would be registered with the driver name
    // defined as in the factory 'DriverName' method.
    if err := repository.RegisterFactory(&Factory{}); err != nil {
        panic(err)
    }
}

// the Factory must implement repository.Factory interface.
var _ repository.Factory = &Factory{}

// Factory is the neuron repository.Factory implementation
// of the custom repository factory.
// Implements repository.Factory.
type Factory {
    instances []*Repository
    ...
}

// DriverName implements repository.Factory interface.
func (f *Factory) DriverName() string {
    return "myfactory"
}

// New implements repository.Factory interface.
func (f *Factory) New(c repository.Controller, model *mapping.ModelStruct) (repository.Repository, error) {
    // Do the logic and create (or use existing) repository instance.
}

// Close implements repository.Factory interface.
func (f *Factory) Close(ctx context.Context, done chan<-interface{}) {
    for _, instance := range f.instances {
        if err := instance.Close(ctx); err != nil {
            // handle the error 
        }
    }
    // send the information that it finished the closing methods.
    done <- struct{}
}
```
The `repository.Factory` is an interface used to create new instances of the `repository.Repository`. This interface requires to implement three methods:

* `DriverName()` - which gets the **unique** driver name for the given factory.
* `New(c Controller,model *mapping.ModelStruct) (repository.Repository, error)`- create new instance of the repository for given 'model'. The `Controller` argument is an interface for the controllers that is used to get model structures. If the Repositories share similar connection configuration it may use the existing instance instead of creating new. The model specific repository configuration are obtainable by using `model.Config().Repository`
* `Close(context.Context, done chan<-interface{})` closes all the instances of the factory related repositories.

## Repository
The Repository is the abstraction used to get the access to the data/service. 
It may be a used as the ORM access to the database, http.Client to some external service or just a custom accessability by using provided interfaces.
The `repository.Repository` is the basic and required interface for the all the repository implementaions. The Repository instances should be created by it's specific Factory. While registering the models, the controller stores the mapping between repositories and related models. All the Repository methods should not be used by the user directly.

In order to access the data or service the Repository may implement specific interfaces defined in the `query` package like:

* `Creator` - creates and stores new instances of the model.
* `Getter` - gets a single instance of the specific model.
* `Lister` - lists multiple instances of the given model.
* `Patcher` - patches only selected model's value.
* `Deleter` - deletes the model instances.
* `Transactioner` - allows to use the transactions.


### Creator
```go
func (r *Repository) (ctx context.Context, scope *query.Scope) error {
    // Do the create logic here
    return nil
}
```
The Create method of the Creator interface requires the Repository to create a specific model instance. For any connection or other relatively long processes the function should check if the '_ctx_' context is not Done yet. Provided '_scope_' structure contains multiple important information 
about given query like:

* `scope.Value` - scope.Value contains the model instance in a pointer to struct type.
* `SelectedFields()` - fields used in the current query.Scope. Only these fields should be stored by this query. It might be useful if a user would like to select and store the zero value fields. If the primary field is not selected, then the Repository should generate new primary field value and set it to the model's scope.Value instance.
* `Struct()` - gets `*mapping.ModelStruct` for the specific query. The model structure has information about all it's fields.

Any error returned by this method should be an instance of `github.com/neuronlabs/neuron-core/errors` `Error` or `MultiError` with defined error classification (``github.com/neuronlabs/neuron-core/errors/class`).

### Getter and Lister
**Getter**
```go
func (r *Repository) Get(ctx context.Context, scope *query.Scope) error {
    // Do the get logic here
    return nil
}
```
**Lister**
```go
func (r *Repository) List(ctx context.Context, scope *query.Scope) error {
    // Do the logic here
    return nil
}
```
Both Get method from the Getter and List from the Lister interface should get a specific model instances for provided query '_scope_'. For any connection or other relatively long processes the function should check if the '_ctx_' context is not Done yet. The get method must not return more than one value instance - (scope.Value is a pointer to the model struct). In order to narrow the scope of the query the `*query.Scope` contains multiple important information like: 

* `PrimaryFilters()`, `AttributeFilters()`, `ForeignKeyFilters()`, `FilterKeyFilters()` - 'scope' methods used to get the specific field's filters. The Repository should not use the `RelationshipFilters()` as the query processor converts them into other filters.
* `Fieldset()` - fieldset is a set of fields returned for each model for given query.
* `Pagination()` - a pagination paramters for given query. Get method must always return a single value, even if the pagination is limited to more than one result.
* `scope.Value` - the value where the results should be stored. It's instance is already initialized and **must not** be overwritten.

Any error returned by this method should be an instance of `github.com/neuronlabs/neuron-core/errors` `Error` or `MultiError` with defined error classification (``github.com/neuronlabs/neuron-core/errors/class`). If there no resultant value was found in related datastore/database then the repository should return an error with _no result_ error classification.

### Patcher
```go
func (r *Repository) Patch(ctx context.Context, scope *query.Scope) error {
    // Do the patch logic here
    return nil
}
```

The Patch method from the Patcher interface should patch models with the values provided by the '_scope_'.  For any connection or other relatively long processes the function should check if the '_ctx_' context is not Done yet.
Provided _scope_ contains a single value instance with specific field values, with related filters used to narrow the query. The patch should update **only** the fields defined in the  scope's _selected fields_. 
Useful parameters stored in the _scope_:

* `scope.Value` - the value where the field's values are stored. It's instance is already initialized and **must not** replaced with new model's instance.
* `PrimaryFilters()`, `AttributeFilters()`, `ForeignKeyFilters()`, `FilterKeyFilters()` - 'scope' methods used to get the specific field's filters. The Repository should not use the `RelationshipFilters()` as the query processor converts them into other filters.
* `SelectedFields()` - fields used in the current query.Scope. Only these fields should be used by this query. It might be useful if a user would like to select and store the zero value fields.

Any error returned by this method should be an instance of `github.com/neuronlabs/neuron-core/errors` `Error` or `MultiError` with defined error classification (``github.com/neuronlabs/neuron-core/errors/class`). If there no resultant value was found in related datastore/database then the repository should return an error with _no result_ error classification.

### Deleter
```go
func (r *Repository) Delete(ctx context.Context, scope *query.Scope) error {\
    // Do the delete logic.
    return nil
}
```

The Delete method from the Deleter interface should delete all the matching model instances defined by the given query _scope_. For any connection or other relatively long processes the function should check if the '_ctx_' context is not Done yet. The _scope_ should contain all the required filters:

* `PrimaryFilters()`, `AttributeFilters()`, `ForeignKeyFilters()`, `FilterKeyFilters()` - 'scope' methods used to get the specific field's filters. The Repository should not use the `RelationshipFilters()` as the query processor converts them into other filters.

Any error returned by this method should be an instance of `github.com/neuronlabs/neuron-core/errors` `Error` or `MultiError` with defined error classification (``github.com/neuronlabs/neuron-core/errors/class`). If no values were affected with given query then the repository should return an error with _no result_ error classification.

## Transactioner

The Transactioner interface requires the Repository to implement transactions for the specific queries. The transaction's should be isolated for the specific Repository (the single repository might be used for multiple models with the same database). The _scope_ has a `Tx()` method which returns the scope's transaction structure. This structure contains the Transaction type and it's unique ID - this value should be used to map the transaction instances.

**Begin**
```go
func (r *Repository) Begin(ctx context.Context, scope *query.Scope) error {
    // Begin the transaction
    return nil
}
```
The Begin method should begin the repository specific transaction. In order to follow up the transactions the Repository on `Begin` should store related transactions 'id' with it's specific structures - i.e. _sql.Tx_ as well as Begin a transaction on it's store/database. The transaction should be created with the specific 'ctx' context.

**Commit**
```go
func (r *Repository) Commit(ctx context.Context, scope *query.Scope) error {
    // Commit the transaction changes
    return nil
}
```
If the query resulted with no error then the transaction should be commited.
The Commit method allows to apply the transaction changes and remove the mapping transaction mapping.

**Rollback**
```go
func (r *Repository) Rollback(ctx context.Context, scope *query.Scope) error {
    // Rollback any changes made by the transation.
    return nil
}
```
If the transaction were rolled back then the `Rollback` should undo the changes made within given transaction, and remove the transaction mapping.

Any error returned by this method should be an instance of `github.com/neuronlabs/neuron-core/errors` `Error` or `MultiError` with defined error classification (``github.com/neuronlabs/neuron-core/errors/class`).
---
nav_order: 1
parent: Core ORM
title: Controller
---

![Logo](/assets/img/logo.svg)

# Controller

Package: `github.com/neuronlabs/neuron-core/controller`

Controller is the structure used by the `neuron-core` that contains all the application context relation data. The `*controller.Controller` contains most important variables used by all the packages in `neuron-core`. It contains mappings - model to structures, model to repository; model definitions, repositories and the configurations.

* [Package](#package)
* [Repositories](#repositories)
* [Models](#models)

## Create Controller

In order to create and set new controller use the `New` or `MustNew` method with the provided config.

```go
package main

import (
    "github.com/neuronlabs/neuron-core/config"
    "github.com/neuronlabs/neuron-core/controller"
)

var cfg *config.Controller

func main() {
    // Read the config
    // cfg = ReadConfig()

    c, err := controller.New(cfg)
    // if the config is not valid the function would return an error.

    // on the other hand creating the config with MustNew would panic on error.
    c2 := controller.MustNew(cfg)
}

```

## Repositories

The `neuron-core` uses the abstraction of the [_repository.Repository_](repositories.md#repository) as a data source. Controller registers and stores them, so that the related models might use their data access.


### Register Repository

The repositories might be stored and registered in two ways:

* With the [Controller Config](config.md#controller)
* By using `RegisterRepository` Controller method

The repository configurations provided with the [_Controller Config_](config.md#controller) would be registered while createing the `New` Controller.

The `RegisterRepository` allows to store manually the [_*config.Repository_](config.md#repository) for the given 'name' within given Controller.

Example:

```go
cfg := &config.Repository{
    Username: testing,
    Password: testing,
    DBName: testing1,
    Port: 5432,
    Host: localhost,
}

err  = c.RegisterRepository("my_repository", cfg)
```

In the example above the following Repository config is stored under the _'my_repository'_ key.

All the model's that are mapped with the _'my_repository'_ Repository, would use the Factory repository credentials shown above.


## Models Mapping

Controller is used to register the models. This creates [_*mapping.ModelStruct_](models.html#structure) for each model, which have a mapping of the [_fields_](models.html#field_structure)

## Registering Models

Model register process should begin **only** when the related **Repositories** are already registered. Each model after being mapped, gets their related [Repository](repository.md#repository) created by it's [_Factory_](repository.md#factory) for it's [Model mapping](model.md#structure)

In order to have an model mapping the models needs to be registered within the controller:

```go
var c *controller.Controller

c := controller.MustGetNew(cfg)

err := c.RegisterModels(Model{}, User{}, Dog{}, Cat{})
...
```
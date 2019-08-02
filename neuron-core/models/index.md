---
nav_order: 3
title: Models
parent: Core ORM
has_children: true
permalink: /neuron-core/models
---

![Logo](/assets/img/logo.svg)

# Models

Package: `github.com/neuronlabs/neuron-core/mapping`

Neuron used struct types with specific fields as the models. 

The model structure were based on the [JSON:API v1.0](https://jsonapi.org) specification. It's design distinguished fields by their kinds. This feature made it easier to isolate the model's by their own repositories.

Neuron maps each model, analyze it's fields with **tags** and creates `*mapping.ModelStruct`. These models are registered in the `Controller`.

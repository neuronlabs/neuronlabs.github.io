---
parent: Models
grand_parent: Core ORM
nav_order: 4
title: Config
---

![Logo](/assets/img/logo.svg)

# Config

While registering the models, each ModelConfig defined under model's `collection` in the `Controller's` ` Config` would be set in the `ModelStruct` definition. 

It's `RepositoryName` value defines to which repository should the model be connected. 

The `Store` field is being copied to the model's store values. This allows to specify repository related variables.

The `AutoMigrate` flag is the information to the repository if the model should be migrated within the repository (database tables etc).
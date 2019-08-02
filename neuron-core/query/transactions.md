---
parent: Query
grand_parent: Core ORM
title: Transactions
nav_order: 12
---

![Logo](/assets/img/logo.svg)

# Transactions

Neuron core is a cloud ready query processor. It's design uses [SAGA](https://microservices.io/patterns/data/saga.html) like transactions in the queries. This concept allows to safe queries along multiple repositories.

Neuron query Scope implements a concept like scope chain. The chain is an array of queries (query.Scope) in an order at which it was executed.
If the root scope uses a transaction, before processing a subscope creates a new transaction and adds itself to the root scope's - scope chain.
On success all scopes stored in the Scope chain sends Commit method, whereas on any failure a Rollback method would be called. The order of these calls is reverse to their position in the slice.

Each repository keeps it's own transaction abstraction. This concept allows to not use a [Two-phase commit](https://en.wikipedia.org/wiki/Two-phase_commit_protocol). Each repository focuses on its own local atomic transaction and other repositories are not blocked if a repository is running for a long time.

* [Begin](#begin)
* [Commit](#commit)
* [Rollback](#rollback)
* [Subscopes](#subscopes)


## Begin

`Begin` is the a method of the `query.Transactioner` interface that begins a transaction for given query. 

## Commit

`Commit` sends approvement of transaction changes to the repository. Implements `query.Transactioner` interface.

## Rollback

`Rollback` rolls back the changes of the given transaction. It is used on transaction failures. Implements `query.Transactioner` interface.

## Subscopes

In order to create a new subscope for given query.Scope use it's `New` method.
It creates a new scope for the provided model. The subscope is added to the root scope chain.
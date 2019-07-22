---
parent: Core ORM
title: Processor
nav_order: 6
---

# Query Processor

Neuron core allows to specify the query processor processes. It implements both goroutine safe (_default_) and unsafe processors. 

The first one gets, patches, deletes the relationships one by one. 
The second does these processes concurently.

The query processor might be set in the `config.Controller`. 

By default it uses thread safe processor (`config.ThreadSafeProcessor`).

In order to change the processor to the concurent - use the `config.ConcurrentProcessor` configuration.


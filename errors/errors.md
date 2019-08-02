---
parent: Errors
title: Error Interfaces and Structs
nav_order: 2
---

![Logo](/assets/img/logo.svg)

## Interfaces 

The package provides simple error handling interfaces and functions.
It allows to create simple and detailed classified errors.

## ClassError

A `ClassError` is the interface that provides error classification with the`Class` method.

## DetailedError

`DetailedError` is the interface used for errors that stores and handles human readable details, contains it's instance id and runtime call operation.
Implements `ClassError`, `Detailer`, `Operationer`, `Indexer`, `error` interfaces.

### Detailer

`Detailer` interface allows to set and get the human readable details - full sentences.

### Operationer

`OperationError` is the interface used to get the runtime operation information.

### Indexer

`Indexer` is the interface used to obtain `ID()` for each error instance.


## Error handling

This package contains two error structure implementations: 

* [Simple Error Structure](#simple-error)
* [Detailed Error Structure](#detailed-error)

### Simple Error Structure

A simple error implements `ClassError` interface. It is lightweight error that contains only a message and it's class.

Created by the `New` and `Newf` functions.

```go
err := New(ClMyCustomClass, "error message")
```

```go
err := Newf(ClMyCustomClass, "formatted: %s", "error message")
```

### Detailed Error Structure

The detailed error struct (`detailedError`) implements `DetailedError`.

It contains a lot of information about given error instance:

* Human readable `Details`
* Runtime function call `Operations`
* Unique error instance `ID` 

In order to create detailed error use the `NewDet` or `NewDetf` functions.

```go
err := NewDet(ClMyCustomClass, "detailed error structure message")=
err.SetDetails("My custom human readable details")
```

```go
err := NewDetf(ClMyCustomClass, "detailed error structure, formatted: %s", "error message")
err.SetDetailsf("My custom human readable formatted details: '%s'", "Message")
```

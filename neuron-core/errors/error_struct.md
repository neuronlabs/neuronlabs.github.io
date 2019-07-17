---
parent: Errors
grand_parent: Core ORM
title: Error Struct
nav_order: 1
---
# Error Struct

Every package in the `neuron-core` returns errors defined in the 
`github.com/neuronlabs/errors`. The structure `*ErrorObject` is created using the `New` and `Newf` methods. Each requires error specific `class.Class` 
and the message. The second function `Newf` allows the message to be formatted with the provided arguments. 

The `*ErrorObject` is composed of `ID` (UUID), `class.Class`, message, detail and an operation at which it occurred.

* `ID` is unique for that specific occurrence UUID identification number
* `Class` defines the error classification
* `Message` is the string returned by the Error method, and is a main error message.
* `Detail` is the human readable, optional detail for the given error occurrence.
* `Operation` is the optional information about given operation

In case if more errors are returned at once, there is a wrapper over the slice of `*ErrorObject` - `MultiError`. By itself it implements the error interface, and combines the error message of multiple errors.
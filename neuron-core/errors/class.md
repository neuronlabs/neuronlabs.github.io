---
parent: Errors
grand_parent: Core ORM
title: Class
nav_order: 2
---
## Class

Due to the requirements of different messages with the same case meaning the rror are distinguished by their classes. The classes are defined in the package `github.com/neuronlabs/errors/class`.

### Comparing errors

Standard golang errors are mostly comparable using the `=` sign.
Each error returned from the `neuron` package functions/methods is the `*ErrorObject` or a slice `MultiError`, 
In order to understand their meaning there is a structure called `class.Class`.

### Class definition

Class is the neuron error classification model.
It is a wrapper over `uint32` composed of the major, minor and index subclassifications.
Each subclassifaction is defined as a different bitwise length number, where the major is composed of 7, minor 10 and index of 15 bits.

Example:
```
 44205263 in a binary form is 00000010101000101000010011001111 which decomposes into:
 
         0000001  - major (7 bit)  - 1
      0101000101  - minor (10 bit) - 325 
 000010011001111  - index (15 bit) - 1231
```

### Class instances

`class` package contains every class instance used by the `neuron-core` functions and methods.
The class might be composed from `Major`, `Minor` or `Index`.

### Major

The `Major` is `uint8` wrapper and is the major error classification. It might be used as a general meaning of the error.
Example:

```
 'Internal' major specifies all the internal errors
```

### Minor

The `Minor` is composed from the [Major](#major). It is more specific subclassification of the errors. 
Example:

```
`InternalQuery` is the minor used to determine internal neuron query errors
```

### Class Index

The `Index` is the most specific error subclassification, created from the [Minors](#minor). 

Example:

```
'InternalQueryFilter' is the classification composed of the 'Filter' index and defines the internal query filter errors.
```


#### Register Classes

In order to expand the possibility of error classification. The class package allows to register [Majors](#major), [Minors](#minor), [Indexes](#class_index) and of course the [Class](#class_definition) structures. 

The package allows to create new Major by providing `RegisterMajor` or `MustRegisterMajor` functions. 

While having the `Major` we can create the `Minor` by using it's register methods. The `Minor` might be used as a `Class` by itself by using `NewMinorClass` or `MustNewMinorClass` functions. 

In order to create the most specific subclassification the `Index` can be created by using `RegisterIndex` or `MustRegisterIndex` methods.

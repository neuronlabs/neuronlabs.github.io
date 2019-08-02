---
parent: Errors
title: Class
nav_order: 1
---

![Logo](/assets/img/logo.svg)

## Comparing errors

Standard golang errors are mostly compared by their messages.

But what if we have multiple errors with the same logic meaning but different messages?

This issue is solved by the `Class` abstraction.

## Class definition

The package defines blazingly fast classification system.
A `Class` is an uint32 wrapper, composed of the `Major`, `Minor` and `Index` subclassifications.
Each subclassifaction has different bitwise length.
A major is composed of 8, minor 10 and index of 14 bits - total 32bits.

Example:

```Class with decimal value of 44205263, in a binary form equals to
 00000010101000101000010011001111 which decomposes into:
 00000010 - major (8 bit)
         1010001010 - minor (10 bit)
                   00010011001111 - index (14 bit)
```

The class concept was inspired by the need of multiple errors
with the same logic but different messages.

## Major 

It is an `uint8` wrapper that store major error subclassification.
Create with `NewMajor` or `MustNewMajor` functions
There might be at most **256** unique Majors.

## Minor

A minor a is mid-level error subclassification. 
It is a `uint16` wrapper. It stores a maximum of 10 bit variable.
Each `Major` may contain **1024** unique minors.

Create using `NewMinor` or `MustNewMinor` functions.

## Index

An index is the lowest level error subclassification.
It is a `uint16` wrapper, with a maximum of 14 bit value.
Each `Minor` may contain **16384** unique indexes.

Create using `NewIndex` or `MustNewIndex` functions.

## New Classes

A class might be composed in three different ways:

* Major only - the class is Major singleton.
* Major, Minor only - classes that don't need triple subclassification divison.
* Major, Minor, Index - classes that decomposes.

---
title: Background
weight: 10
---

# Background

This part of the tutorial reviews important concepts
which are underlying the changes in Java 17 discussed later.
In typed functional languages
[algebraic data types](data)
are used to define different kinds of values,
and [pattern matching](patterns)
is used to inspect these values when defining functions.

These two concepts are linked in an important way:
by comparing patterns in function definitions with
the definitions of corresponsing data types
compilers can check if functions may fail to process their inputs.
As a result, compilers can guarantee that functions cannot fail
if their patterns are exhaustive.

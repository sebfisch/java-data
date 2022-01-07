---
title: Record Patterns
weight: 26
---

# Record Patterns

[Record Patterns](https://openjdk.java.net/jeps/405)
are not available in Java 17,
but are expected to be provided in some later version.
We briefly mention them anyway 
because they represent an important aspect of pattern matching
discussed in the background section:
[deconstructing product types](../../background/patterns/#deconstructing-product-types).

With appropriate definitions for three-argument functions `min` and `max`,
we could[^double] implement a `saturation` method for `RGBColor` values as follows.

[^double]: We may have to change the types of the fields to integral values 
because floats and doubles cannot yet be inspected in `switch` constructs.
However, allowing floats and doubles is planned as future work
and might be available when record patterns are released.

```java
    public double saturation() {
        return switch (this) {
            case RGBColor(var r, var g, var b) && max(r,g,b) == 0
                -> 0;
            case RGBColor(var r, var g, var b)
                -> (max(r,g,b) - min(r,g,b)) / max(r,g,b);
        };
    }
```

In this definition, `RGBColor(var r, var g, var b)` is a record pattern
introducing pattern variables `r`, `g`, and `b`
which are bound to the values of the corresponding record fields
`red`, `green`, and `blue` of the matched `RGBColor` value.

The ability to deconstruct records directly inside a pattern
would also be useful to simplify the definitions
of our default methods for optional values and recursive lists
because we would not have to introduce the variable `self`.

Record patterns will be applicable in `switch` constructs
as well as `instanceof` checks.
In more complex scenarios they can be nested
to deconstruct deeply nested values.
In the example above, 
the nested patterns are expressed using
[Local-Variable Type Inference](https://openjdk.java.net/jeps/286)
without mentioning corresponding types explicitly.

In addition to record patterns, array patterns will be introduced
to provide the ability to deconstruct arrays.

---
title: Record Patterns
weight: 26
---

# Record Patterns

[Record Patterns](https://openjdk.org/jeps/440)
represent an important aspect of pattern matching
discussed in the background section:
[deconstructing product types](../../background/patterns/#deconstructing-product-types).

With appropriate definitions for three-argument functions `min` and `max`,
we can implement a `saturation` method for `RGBColor` values as follows.

```java
public double saturation() {
    return switch (this) {
        case RGBColor(var r, var g, var b) when max(r, g, b) == 0 -> 0;
        case RGBColor(var r, var g, var b) -> (max(r, g, b) - min(r, g, b)) / max(r, g, b);
    };
}
```

In this definition, `RGBColor(var r, var g, var b)` is a record pattern
introducing pattern variables `r`, `g`, and `b`
which are bound to the values of the corresponding record fields
`red`, `green`, and `blue` of the matched `RGBColor` value.

The ability to deconstruct records directly inside a pattern
is also useful to simplify the definitions
of our default methods for optional values and recursive lists
because we do not have to introduce the variable `self`.

Here are adjusted definitions of `map` and `filter` for our `Optional` type:

```java
default <U> Optional<U> map(Function<T, U> fun) {
    return switch (this) {
        case Empty() -> new Empty<>();
        case Present(var value) -> new Present<>(fun.apply(value));
    };
}

default Optional<T> filter(Predicate<T> pred) {
    return switch (this) {
        case Empty() -> this;
        case Present(var value) when pred.test(value) -> this;
        case Present(var value) -> new Empty<>();
    };
}
```

You can rewrite your solution to 
[Task 5: refactor recursive lists](../switchpatterns/#task-5-refactor-recursive-lists)
to use record patterns as well.

Record patterns are available in `switch` constructs
as well as `instanceof` checks.
In more complex scenarios they can be nested
to deconstruct deeply nested values.
In the example above, 
the nested patterns are expressed using
[Local-Variable Type Inference](https://openjdk.java.net/jeps/286)
without mentioning corresponding types explicitly.

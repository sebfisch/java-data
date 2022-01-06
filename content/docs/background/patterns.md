---
title: Pattern Matching
weight: 14
---

# Pattern Matching

Pattern matching provides a way to inspect algebraic datatypes
and access the components of their variants.

## Branching on enum types

The following pseudo code
describes the behavior of a function `asHexString`
which computes a hexadecimal representation of a `BaseColor` value.

```
asHexString(RED) -> "#ff0000"
asHexString(GREEN) -> "#00ff00"
asHexString(BLUE) -> "#0000ff"
```

`BaseColor` was defined as an enum type,
so we can use a switch statement to implement an `asHexString` method.

```java
public enum BaseColor { 
    RED, GREEN, BLUE;

    public static String asHexString(BaseColor color) {
        switch (color) {
            case RED: return "#ff0000";
            case GREEN: return "#00ff00";
            case BLUE: return "#0000ff";
            default: throw new IllegalArgumentException("unreachable");
        }
    }
}
```

The different cases in the switch statement correspond to the different clauses
in the preceding description.
We need to include an unreachable default case
to satisfy the compiler which
(with this definition)
does not detect that all cases are handled.

## Branching on sum types

What if the argument type of the function is not an enum type?
At least in pseudo code, we can still use pattern matching 
to describe functions on values of algebraic data types.
For example, we can describe the `map` function on optional values as follows.

```
Empty().map(fun) -> Empty()
Present(value).map(fun) -> Present(fun.apply(value))
```

We can implement `map` for our optional type in different ways.
One possibility is to add `map` to `BasicOptional`
and implement this method in all nested classes.
Here is an adjusted definition of `BasicOptional`.

```java
public interface BasicOptional<T> {
    <U> BasicOptional<U> map(Function<T,U> fun);

    static class Empty<T> implements BasicOptional<T> {
        public <U> BasicOptional<U> map(Function<T,U> fun) {
            return new Empty<>();
        }
    }

    static class Present<T> implements BasicOptional<T> {
        public <U> BasicOptional<U> map(Function<T,U> fun) {
            return new Present<>(fun.apply(this.value()));
        }

        // rest of the implementation as shown before
    }
}
```

The `map` function takes a
[Function](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/function/Function.html)
as argument and creates a new optional value from the one it is called on.
If a value is present, 
the given function is applied to it to compute the result of `map`.

This implementation of `map` is difficult to understand as a whole
because it is scattered through the definitions of the nested classes.
As an alternative, we can provide a
[Default Implementation](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)
in `BasicOptional` which handles all cases in one place.

```java
public interface BasicOptional<T> {
    default <U> BasicOptional<U> map(Function<T,U> fun) {
        if (this instanceof Empty) {
            return new Empty<>();
        }

        if (this instanceof Present) {
            final Present<T> self = (Present<T>) this;
            return new Present<>(fun.apply(self.value()));
        }

        throw new IllegalStateException("neither empty nor present");
    }

    // nested classes (without `map`) as shown before
}
```

While this implementation has the advantage 
that all variants of optional values are handled together
it also has several disadvantages:
  * It is cumbersome to access a present value because of a required type cast.
  * The compiler cannot detect if we have handled all possible cases.

## Deconstructing product types

Apart from distinguishing different possible variants of matched values,
pattern matching is also used to access components of combined values.
As another example in pseudo code,
consider this description of a saturation function for RGB colors.

```
BasicRGBColor(r,g,b).saturation() -> 0 if max(r,g,b) = 0
BasicRGBColor(r,g,b).saturation() -> (max(r,g,b) - min(r,g,b)) / max(r,g,b)
```

Both clauses match the same kind of value (a `BasicRGBColor`)
but the first clause has an attached condition
which specifies that it is only applicable 
if all (non-negative) values for the base colors are zero.
The second clause applies only if this condition is false.
Both clauses introduce variables `r`, `g`, and `b`
for the values of the color components
which are used to compute the result of the function `saturation`.

If we implement `saturation` as a method of `BasicRGBColor`
we can access the private fields `red`, `green`, and `blue`
storing the different color components.

```java
    public double saturation() {
        final double max = Math.max(red, Math.max(green, blue));

        if (max == 0) {
            return 0;
        }

        final double min = Math.min(red, Math.min(green, blue));

        return (max - min) / max;
    }
```

When matching values of product types that are variants of a sum type
we might want to match a combined value outside of its defining class
like in our `map` function for optional values.
In that case, the fields `red`, `green`, and `blue` would not be available,
and we would have to use corresponding methods to access them.

As a final example we discuss the `map` function for recursive lists,
which computes a new list of the same size
and applies a given function to each old element
to get the elements of the new list.
Here is a description of this function in pseudo code.

```
Empty().map(fun) -> Empty()
Populated(head,tail).map(fun) -> Populated(fun.apply(head),tail.map(fun))
```

Note that this `map` function is recursive
because it is called recursively on the tail of a populated list.
When defining `map` as a default method in `BasicRecursiveList`
we can handle all cases in one place
and access the private fields for the head and tail of a populated list.

```java
    default <U> BasicRecursiveList<U> map(Function<T,U> fun) {
        if (this instanceof Empty) {
            return new Empty<>();
        }

        if (this instanceof Populated) {
            final Populated<T> self = (Populated<T>) this;
            return new Populated<>(fun.apply(self.head), self.tail.map(fun));
        }

        throw new IllegalStateException("neither empty nor populated");
    }
```

Again, this implementation is cumbersome because of a required type cast
and because the compiler cannot detect that we have handled all cases.

This concludes the background section.
We are now ready to discuss [New Features](../../features) in Java 17.

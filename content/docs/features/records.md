---
title: Records
weight: 22
---

# Records

[Records](https://openjdk.java.net/jeps/395)
are motivated by the desire to implement product types
more consisely than is otherwise possible.
They are equivalent to final classes without an extends clause
(their direct super-class is always `Record`)
and where all fields are `private` and `final`.
Record definitions automatically provide canonical constructors,
field declarations, and corresponding methods for read access.
They also provide sensible implementations
for the `equals`, `hashCode`, and `toString` methods.

Another feature introduced together with records are
local definitions for records, enums or interfaces,
which we discuss below.

## RGB colors

The following definition can be used like `BasicRGBColor` 
(without the `saturation` method)
defined previously.

```java
public record RGBColor(double red, double green, double blue) {}
```

No constructors, fields and methods for read access are defined explicitly.
But they are defined implicitly by this declaration.
as well as `equals`, `hashCode` and `toString` methods,
which we previously omitted for brevity.

We could add additional constructors or methods (like `saturation`)
or provide alternative implementations for generated constructors or methods.
For example, we can provide our own definition for the constructor,
which checks that all provided color values are between zero and one.

```java
public record RGBColor(double red, double green, double blue) {
    public RGBColor {
        check(red);
        check(green);
        check(blue);
    }

    private static void check(double val) {
        if (val < 0 || 1 < val) {
            throw new IllegalArgumentException("invalid color value " + val);
        }
    }
}
```

Note that the signature of the defined constructor does not declare parameters,
and its body does not contain assignments to the fields of the record.
The parameter list is implicitly copied from the record declaration,
and assignments of the parameters to the record fields are implicitly included
at the end of the constructor body.
Note that the parameters are not declared final,
so we could change their values, for example, to normalize them
before they are assigned to the fields.

## Optional values

The following definition simplifies our previous definitions for optional values 
using records for their different variants.

```java
public sealed interface Optional<T> {
    record Empty<T>() implements Optional<T> {}
    record Present<T>(T value) implements Optional<T> {}

    // default method `map` implemented as before
}
```

We omit the `permits` clause, which is inferred from the nested records.
Note that records are implicitly final, 
so we do not need to explicitly declare them as such,
although they implement a sealed interface.
They are implicitly static if they are nested.
Also note that, 
while records are not allowed to declare an explicit super-class,
they can implement interfaces and inherit their default methods.

### Task 1: add null check for optionals

Provide an explicit constructor for `Optional.Present`
that rejects passed `null` values
by throwing an `IllegalArgumentException`.
Add an additional test to `OptionalTests`
that checks that the null check works as intended.

## Local declarations

Records are often useful as containers for intermediate results.
We could define records as nested classes and use them like other nested classes.
But records (as well as enums and interfaces) can now also be defined locally
inside a method.
When we define records like this, 
their definition is closer to where they are used,
and it is not visible outside the method that contains it,
so it does not clutter the namespace.

Here is a static method inside `RGBColor` that demonstrates this technique.

```java
    public static Optional<RGBColor> findBrightest(List<RGBColor> colors) {
        record AugmentedColor(RGBColor color, double brightness) {
            AugmentedColor(RGBColor color) {
                this(color, (color.red + color.green + color.blue) / 3);
            }
        }

        return colors.stream()
                .map(AugmentedColor::new)
                .max(Comparator.comparing(AugmentedColor::brightness))
                .map(AugmentedColor::color);
    }
```

The method `findBrightest` returns one of the brightest colors
in the list given as argument.
The result is returned as an optional value
using the
[Optional](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Optional.html)
class predefined in Java, 
not using our own implementation of optional values.

The record `AugmentedColor` is defined locally inside `findBrightest`.
It has an additional constructor that only takes a color as argument
and computes the second argument of the canonical constructor.
As a result, 
the brightness of an augmented color does not have to be recomputed
if it is accessed repeatedly.
The constructor does not replace the canonical constructor,
so it has an explicit list of parameters.

The result of `findBrightest` is computed using a
[Stream](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Stream.html)
pipeline.
First, the colors are augmented using `Stream.map` 
and the construtor of the local record.
Then, the brightest augmented color is computed using `Stream.max`
and the `brightness` method of the local record
which provides read access to the pre-computed brightness.
Finally, the brightness is discarded using `Optional.map`
and the `color` method of the local record.
The arguments of `map` and `comparing` are provided as
[Method References](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html).

## Task 2: implement recursive lists

Define a sealed interface `RecursiveList<T>` 
with nested records `Empty<T>` and `Populated<T>` for their variants.
Define a test class `RecursiveListTests`
which checks that the list implementation works as intended.

## Task 3: solve advent of code task

[Advent of Code 2021, Day 2](https://adventofcode.com/2021/day/2)
poses a task where the position of a submarine is adjusted
based on a list of commands operating it.

Solve the task 
by applying what you have learned about algebraic datatypes so far.
Please refrain from using language features for pattern matching
discussed later in this tutorial.
There will be another task to make use of those additional features.

You are encouraged to solve the task using your own design.
If you want guidance,
you can solve this task by defining an algebraic datatype `Command`
(as a sealed interface with nested records for the different variants)
and a mutable class `Position` with a method `moveBy(Command)`
that adjusts the fields of a position according to the given command.


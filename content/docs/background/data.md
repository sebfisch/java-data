---
title: Algebraic Datatypes
weight: 12
---

# Algebraic Datatypes

Algebraic datatypes are a combination of sum types and product types
which we discuss next.

## Sum types

Sum types are used to represent different variants of values.
They are also called variant types or tagged union types.
A simple example of a sum type would be a type
where corresponding values are limited to one of the three base colors:
red, green, and blue.

Types where the number of possible values is finite can be defined as
[Enum Types](https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html)
in Java.
For example, the following definition represents base colors as an enum.

```java
public enum BaseColor { RED, GREEN, BLUE }
```

However, sum types are more general than enum types.
They can be used to define types with an unbounded number of values
which correspond to a finite number of variants.

As a basic example
consider a type for optional values which can be empty or present.
Present optional values contain an arbitrary value. 
As a result, the number of optional values is not finite,
so we cannot represent them as an enum in Java.

But we can define a class hierarchy
to represent different variants of optional values.
The following definition (partially) reimplements
[Optionals](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Optional.html)
in Java by defining a corresponding interface
together with two nested classes that implement it
to represent the two variants of optional values.

```java
public interface BasicOptional<T> {
    class Empty<T> implements BasicOptional<T> {}

    class Present<T> implements BasicOptional<T> {
        private final T value;

        public Present(T value) {
            this.value = value;
        }

        public T value() {
            return value;
        }
    }
}
```

Like `Optional`, `BasicOptional` is a
[Generic Type](https://docs.oracle.com/javase/tutorial/java/generics/types.html)
with a type parameter for the wrapped values.
In the definitions of the nested classes
we omit definitions for the `equals`, `hashCode`, and `toString` methods
for brevity.

## Product types

Product types are used to represent values that combine values of multiple types.
Tuple types and record types are examples of product types,
where record types assign names to the different components
and tuple types do not.
As an example for a product type consider a type for RGB colors
combining color values for each of the base colors red, green, and blue.

In Java we can define product types as classes with multiple fields.
For example, the following class represents RGB color values.

```java
public class BasicRGBColor {
    private final double red;
    private final double green;
    private final double blue;

    public BasicRGBColor(double red, double green, double blue) {
        this.red = red;
        this.green = green;
        this.blue = blue;
    }

    public double red() {
        return red;
    }

    public double green() {
        return green;
    }

    public double blue() {
        return blue;
    }
}
```

The `BasicRGBColor` class defines three fields for color values
and corresponding methods to access the values of those fields
but no methods to change them.
Again, we omit `equals`, `hashCode`, and `toString` definitions for brevity.

## Sums of products and recursive types

Algebraic datatypes are a combination of sum and product types.
They can be used to represent different variants of values
where each variant can combine values of multiple types.

In functional languages it is common practice to define recursive types
where the definition may refer to the defined type
in some of the components of some variants.
As an example for a recursive type that combines sum and product types
consider the following definition for recursive lists.

```java
public interface BasicRecursiveList<T> {
    class Empty<T> implements BasicRecursiveList<T> {}

    class Populated<T> implements BasicRecursiveList<T> {
        private final T head;
        private final BasicRecursiveList<T> tail;

        public Populated(T head, BasicRecursiveList<T> tail) {
            this.head = head;
            this.tail = tail;
        }

        public T head() {
            return head;
        }

        public BasicRecursiveList<T> tail() {
            return tail;
        }
    }
}
```

`BasicRecursiveList` is a sum type 
because there are variants for empty and populated lists.
`Populated` is a product type
because there are components for the head and the tail of a populated list.
The definition is recursive
because the nested class `Populated` 
refers to the surrounding type `BasicRecursiveList` for the tail component.
For brevity, we omit `equals`, `hashCode`, and `toString` methods
in the nested classes.

We have seen that it is possible to define algebraic datatypes in Java
without using any new features.
However, the shown definitions have drawbacks that we will discuss later
and they are quite verbose 
even if we omit advisable definitions for `equals`, `hashCode`, and `toString`.

In the next section we discuss
[pattern matching](../patterns)
as a concise way to define functions that take arguments of algebraic datatypes.

---
title: Sealed Classes
weight: 21
---

# Sealed Classes

[Sealed Classes](https://openjdk.java.net/jeps/409)
are motivated by the desire to define sum types
where the class hierarchy is fixed 
and not intended to be extended by others.
Using the new keywords `sealed` and `permits`
classes (and interfaces) can restrict
which other classes can extend (or implement) them.

Sealed classes provide a middle ground between normal classes,
which can be extended without restrictions,
and final classes, which cannot be extended at all.
They provide a basis for ehaustiveness checking during
[pattern matching for `switch`](../switchpatterns)
which helps to avoid unreachable branches
and to ensure that all possible branches are defined.

Sealed classes also help the compiler to reject certain checks using `instanceof`
if the restrictions implied by the `sealed` or `final` keywords
make the check impossible to succeed.

## Optional values

Here is an adjusted definition for optional values using a sealed interface.

```java
public sealed interface SealedOptional<T>
    permits SealedOptional.Empty<T>, SealedOptional.Present<T>
{
    final class Empty<T> implements SealedOptional<T> {}

    final class Present<T> implements SealedOptional<T> {
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

This definition resembles the previous one with the following differences:
  * The `sealed` keyword marks the interface as sealed.
  * The `permits` keyword starts a list of allowed sub-classes.
  * The nested classes are declared as `final` to prevent further sub-classes.

## Restrictions

It is an error to define a class extending a sealed class
(or implementing a sealed interface)
if the sub-class is not listed in the `permits` clause.
It is also an error 
if a class mentioned in the `permits` clause of a sealed class (or interface)
does not extend that class (or implement that interface) directly.
IDEs might be able to resolve these errors automatically.
Permitted sub-classes must be defined in the same module
(or, if declared in an unnamed module, in the same package)
as their super-class.

In the definition above, where all allowed sub-classes are nested classes,
the `permits` clause is optional and would be inferred if it is omitted.
In other cases, without nested sub-classes, 
a missing (or empty) `permits` clause is not allowed.

All classes extending sealed classes (or implementing sealed interfaces)
have to be declared either `final`, `sealed`, or `non-sealed`
to explicity control further sub-classes.
When the new keyword `non-sealed` is used, 
the sub-class could be extended by arbitrary other classes
(just like normal classes)
breaking the seal of the super-class.

We now look at [Records](../records) 
which help to further simplify our definition of optional values.

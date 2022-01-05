---
title: Pattern Matching for instanceof
weight: 23
---

# Pattern Matching for `instanceof`

[Pattern Matching for `instanceof`](https://openjdk.java.net/jeps/394)
helps to avoid type casts when we know that they succeed.

## Matching optional values

For example, we can use this feature 
to simplify the definition of `map` for optional values.

```java
    default <U> Optional<U> map(Function<T, U> fun) {
        if (this instanceof Present<T> self) {
            return new Present<>(fun.apply(self.value));
        }

        return new Empty<>();
    }
```

This definition differs from the previous one in two ways.
Firstly, the condition using `instanceof` introduces a variable `self`
of type `Optional.Present<T>` so we do not need to cast `this` explicitly.
Secondly, we do not throw an `IllegalStateException` for `Optional` instances
that are neither `Empty` nor `Present`, 
because we know that such instances cannot exist because `Optional` is sealed.

## Flow scoping

The scope of the `self` variable is determined using flow scoping.
As a consequence, `self` is in scope 
where the compiler than guarantee that the `instanceof` check is successful.

Here is an alternative definition of `map` for optional values 
that demonstrates flow scoping.

```java
    default <U> Optional<U> map(Function<T, U> fun) {
        if (!(this instanceof Present<T> self)) {
            return new Empty<>();
        }
        
        return new Present<>(fun.apply(self.value));
    }
```

The condition is now negated, and as a result 
`self` is in scope after the `if` statement
where we only get if its condition is `false`
due to the `return` statement in its body.

As another example for flow scoping,
consider a `filter` function for optional values,
which can be described using pseudo code as follows.

```
Empty().filter(pred) -> Empty()
Present(value).filter(pred) -> Present(value) if pred.test(value)
Present(value).filter(pred) -> Empty()
```

The `filter` function takes a
[Predicate](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/function/Predicate.html)
as argument and uses it to test a present value.
If the value of a present optional passes the test
the optional value is returned unchanged.
In all other cases the result of `filter` is `Empty`.

We can define this function as a default method for `Optional`
by using an `instanceof` check with a pattern variable.

```java
    default Optional<T> filter(Predicate<T> pred) {
        if (this instanceof Present<T> self && pred.test(self.value)) {
            return this;
        }

        return new Empty<>();
    }
```

Note that due to flow scoping, 
the pattern variable `self` is in scope in the second argument of `&&`
because this part of the condition is only executed 
when the first part of the condition is `true`.

## Task 4: expand recursive lists

Implement `length`, `map`, and `filter` functions
as default methods of the sealed interface `RecursiveList`
defined in a [previous task](../records/#task-2-implement-recursive-lists).

Here are pseudo-code descriptions for those functions.

```
Empty().length() -> 0
Populated(_,tail).length() -> 1 + tail.length()

Empty().map(fun) -> Empty()
Populated(head,tail).map(fun) -> Populated(fun.apply(head), tail.map(fun))

Empty().filter(pred) -> Empty()
Populated(h,t).filter(p) -> Populated(h,t.filter(p)) if p.test(h)
Populated(h,t).filter(p) -> t.filter(p)
```

Use patterns in `instanceof` checks where possible.
Add tests to `RecursiveListTests` that check that the implemented functions
work as intended.

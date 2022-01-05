---
title: Switch Expressions
weight: 24
---

# Switch Expressions

[Switch Expressions](https://openjdk.java.net/jeps/361)
expand the ways `switch` can be used in Java.
It is now possible to use the `switch` construct as an expression
(that has a value)
instead of using it as a statement (that has an effect.)

Switch expressions are introduced together with arrow labels,
which is an independent feature for alternativ control flow.
Unlike the traditional fall-through behavior,
`switch` constructs with arrow labels execute only one of their cases.

Combining these two novelties, there are now four kinds of `switch` constructs:
  * `switch` statements with fall-through behavior (the traditional kind)
  * `switch` statements without fall-through behavior
  * `switch` expressions with fall-through behavior
  * `switch` expressions without fall-through behavior

Switch expressions must be exhaustive, and for enum types
the compiler checks that they are
when no default clause is given.

## Arrow labels

We can rewrite the `BaseColor.asHexString` method from a
[previous section](../../background/patterns/#matching-for-enum-types)
using a `switch` expression with arrow labels.

```java
    public static String asHexString(BaseColor color) {
        return switch (color) {
            case RED -> "#ff0000";
            case GREEN -> "#00ff00";
            case BLUE -> "#0000ff";
        };
    }
```

Unlike the previous version, this definition does not need a default case,
because the compiler detects that all possible cases are handled.
While adding an unreachable default case would still be possible,
it is not advisable,
because the compiler would not warn us 
when new enum values are added without adjusting the definition of `asHexString`.

Arrow labels can also be used with `switch` statements,
rather than expressions.

```java
    public static String describe(BaseColor color) {
        switch (color) {
            case RED -> {
                return "hot";
            }
            case GREEN, BLUE -> {
                return "cold";
            }
            default -> throw new IllegalArgumentException("unreachable");
        }
    }
```

The second case lists two enum values 
because arrow labels do not provide fall-through behavior.
Unlike in `switch` expressions,
we have to include a default case in `switch` statements
also when using arrow labels,
even if it is unreachable.
As a result, the compiler cannot warn us about missing cases,
when new enum values are added.

## `yield` statement

The new `yield` statements can be used to write `switch` expressions
with blocks in their branches.
Traditional `case` labels are always followed by a block,
so `yield` is necessary to define a `switch` expression 
with fall-through behavior.
Here is an alternative definition of `describe`
using fall-through behavior and `yield`.

```java
    public static String describe(BaseColor color) {
        return switch (color) {
            case RED:
                yield "hot";
            case GREEN:
            case BLUE:
                yield "cold";
        };
    }
```

We do not need a default case in an exhaustive `switch` expression,
even if it uses traditional `case` labels.
In `switch` expressions with arrow labels, we can also use blocks,
for example, for intermediate computations,
but they have to be enclosed in curly braces.
The following version of `describe` uses `yield` in a block
for a `case` branch with an error label.

```java
    public static String describe(BaseColor color) {
        return switch (color) {
            case RED -> "hot";
            case GREEN, BLUE -> {
                final String cold = "c" + "o" + "l" + "d";
                yield cold;
            }
        };
    }
```

The
[source code](https://github.com/sebfisch/java-data-code/blob/latest/src/main/java/sebfisch/colors/BaseColor.java)
for `BaseColor` contains even more variants of the `describe` function
including the canonical one using a `switch` expression with arrow labels
and no `yield`.

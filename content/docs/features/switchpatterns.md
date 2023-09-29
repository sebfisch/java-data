---
title: Pattern Matching for switch
weight: 25
---

# Pattern Matching for `switch`

[Pattern matching for `switch`](https://openjdk.org/jeps/441)
significantly expands the types of values
that can be inspected in a `switch` construct
and the ways in which they can be inspected.

Traditionally,
`switch` statements can only inspect 
  * primitive integral numbers,
  * their boxed versions,
  * strings, or
  * enum values,

and inspecting `null` always leads to a `NullPointerException`.
Now, arbitrary reference types can be inspected using type patterns
and `null` checks can be incorporated into `switch` constructs.
Guarded patterns allow to combine patterns with boolean expressions.

## Type patterns

Type patterns,
as introduced with [Pattern Matching for `instanceof`](../instanceof),
can now also be used in `switch` constructs.
We can use them to further improve the implementation 
of our `map` function for optional values.

```java
default <U> Optional<U> map(Function<T, U> fun) {
    return switch (this) {
        case Empty<T> self -> new Empty<>();
        case Present<T> self -> new Present<>(fun.apply(self.value));
    };
}
```

This implementation is an improvement over the one using `instanceof`
because the compiler can check that we have covered all cases.
Type patterns use the notion of type coverage
in order to determine that a `switch` expression is exhaustive.
This notion inspects permitted instances of sealed classes and interfaces
to determine if a `switch` expression without default case
(like the one shown above)
is exhaustive.

When using patterns in `switch` statements,
they are also required to be exhaustive (like `switch` expressions.)
For backwards compatibility,
this requirement is not extended to traditional switch statements,
but compilers may generate a warning 
for incomplete traditional `switch` statements.

## Guarded patterns

The scope of variables introduced in type patterns 
is determined using flow scoping just like with `instanceof`.
Here is an improved implementation of the `filter` function for optional values.

```java
default Optional<T> filter(Predicate<T> pred) {
    return switch (this) {
        case Empty<T> self -> self;
        case Present<T> self when pred.test(self.value) -> self;
        case Present<T> self -> new Empty<>();
    };
}
```

Just like with `map`, we benefit from an exhaustiveness check by the compiler.

Note that there are two cases for present values.
The first one uses a guarded pattern,
which combines a pattern with a boolean expression.
The condition is only evaluated if the pattern to the left of the keyword `when` matches.
Consequently, the pattern variable `self` is in scope in the condition
as well as in the right hand side of the `case` branch.
The pattern in the second `case` branch for present values is not guarded.
It is less restrictive than the previous one and, therefore, said to dominate it.
As patterns are tested top down, the last branch only matches 
if the present value is not accepted by the filter predicate.

It would be compile time error 
to change the order of the two branches for present values,
because the guarded pattern would be dominated 
by the pattern in the previous branch and, therefore,
the branch with the guarded pattern would be unreachable.
This check helps avoid unreachable branches,
but it does not prevent them completely.
For example, we could add a `default` branch
at the end of the `switch` expression
without producing a compile-time error.
While such a branch is dominated by the previous branches together,
it is not dominated by a single previous branch and, currently,
not flagged by the compiler.

The are additional subtleties involving flow scoping and fall-through behaviour.
Interested readers are referred to the description of the language feature
linked at the top of this page for details.

## Task 5: refactor recursive lists

Refactor your implementations of the `length`, `map`, and `filter` functions
for recurslive lists to use pattern matching in `switch` expressions.
Make sure that your tests still pass after your refactoring.

## `null` patterns

Traditional `switch` constructs throw a `NullPointerException`
when inspecting the `null` value.
The new `switch` construct retains this behavior in some cases.
As an example, consider the following function on `RGBColor` values.

```java
public static String describe(RGBColor color) {
    return switch (color) {
        case RGBColor c && c.red > c.green && c.red > c.blue -> "reddish";
        case RGBColor c && c.green > c.red && c.green > c.blue -> "greenish";
        case RGBColor c && c.blue > c.red && c.blue > c.green -> "blueish";
        default -> "other";
    };
}
```

This function uses multiple guarded patterns to determine a color tendency
and a default branch that matches if all color values are the same.
The call `describe(null)` throws a `NullPointerException`,
because the default branch does not match `null` for backwards compatibility.

Here is an alternative definition that handles `null` values.

```java
public static String describe(RGBColor color) {
    return switch (color) {
        case RGBColor c && c.red > c.green && c.red > c.blue -> "reddish";
        case RGBColor c && c.green > c.red && c.green > c.blue -> "greenish";
        case RGBColor c && c.blue > c.red && c.blue > c.green -> "blueish";
        case null, default -> "other";
    };
}
```
This definition uses a `null` pattern and the new `default` case label
to handle both cases in a single branch.
With this definition the call `describe(null)` returns `"other"`.

Here is an alternative implementation with the same behavior.

```java
  public static String describeWithTotalBranch(RGBColor color) {
    return switch (color) {
      case RGBColor c when c.red > c.green && c.red > c.blue -> "reddish";
      case RGBColor c when c.green > c.red && c.green > c.blue -> "greenish";
      case RGBColor c when c.blue > c.red && c.blue > c.green -> "blueish";
      case RGBColor c -> "other";
      case null -> "other";
    };
  }
```

The result of the call `describe(null)` is `"other"`,
like with the previous definition.

Note that the total type pattern in the end does not match the `null` value,
so we add an additional case using the `null` pattern.

## Task 6: refactor AoC solution

Refactor your solution
(or [mine](https://github.com/sebfisch/java-data-code/blob/solutions/src/main/java/sebfisch/aoc21/day02/Position.java)) 
of the
[Advent of Code task](../records/#task-3-solve-advent-of-code-task)
to use pattern matching for `instanceof` or `switch` expressions.
Check your IDE for code cleanup operations related to the new Java features,
to see if it can help you with this task.

Bonus: Extend the position with an aim attribute
that is influenced by up- and down-commands 
instead of influencing the depth directly:

```
pos.moveBy(Up(steps)):
    pos.aim -= steps
pos.moveBy(Down(steps)):
    pos.aim += steps
pos.moveBy(Forward(steps)):
    pos.horizontal += steps
    pos.depth += pos.aim * steps
```

The forward command now also affects the submarines depth based on its aim.

To conclude the pattern-matching discussions,
we look at [Record Patterns](../recordpatterns).

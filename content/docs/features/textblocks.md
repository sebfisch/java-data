---
title: Text Blocks
weight: 27
---

# Text Blocks

[Text Blocks](https://openjdk.java.net/jeps/378)
provide a new way to write string literals in Java.
They have nothing to do with algebraic datatypes or pattern matching,
but we mention them anyway to complete our tour 
of new language features in Java 17 introduced by
[Project Amber](https://openjdk.java.net/projects/amber/)
since the previous LTS release.

Here is a definition from my solution to the
[Advent of Code task](../records/#task-3-solve-advent-of-code-task)
that uses a text block for the example input.

```java
    static Stream<Command> exampleInput() {
        return
            """
            forward 5
            down 5
            forward 8
            up 3
            down 8
            forward 2
            """.lines().map(Command::parse);
    }
```

Text blocks allow to write multi-line strings 
without escape sequences and string concatenation.
They are delimited by `"""` and can contain `"` characters without escaping them.
Incidental whitespace,
which is the longest common whitespace prefix of all lines in the text block,
is stripped from the beginnings of all lines automatically.

A new escape sequence consisting of `\` and a line terminator 
can be used in text blocks to avoid introducing newline characters.
The new escape sequence `\s` for a space character can be used 
at the end of lines in text blocks
to create lines with spaces at the end.
Trailing whitespace characters that are not written using escape sequences
will be removed automatically.

Text blocks can be concatenated just like normal strings,
but doing that gets unwieldy quickly.
Instead of string concatenation,
programmers can use value substitution using the static method `String.format`
or the new equivalent instance method `String.formatted`.

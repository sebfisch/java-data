---
title: "Algebraic Datatypes and Pattern Matching in Java"
---

# Algebraic Datatypes and Pattern Matching in Java

> The problem with object-oriented languages is 
> they’ve got all this implicit environment that they carry around with them. 
> You wanted a banana but what you got was a gorilla holding the banana
> and the entire jungle.
>
> --- [Joe Armstrong](https://en.wikipedia.org/wiki/Joe_Armstrong_(programmer))
> (✝︎2019) co-designer of the
> [Erlang programming language](https://www.erlang.org/)
> in
> [Coders at Work](https://codersatwork.com/)

Objects are
[coupling](https://en.wikipedia.org/wiki/Coupling_(computer_programming))
data with operations.
While this can be beneficial for the design of large software systems
---for example, to implement
[Information Hiding](https://en.wikipedia.org/wiki/Information_hiding)
by carefully selecting available operations to manipulate data---
in practice, it is often beneficial 
to separate operations from the data they operate on,
in order to make them more reusable and easier to test.

In object-oriented languages,
the result of methods is not only determined by its arguments
but, possibly, also by the local state of the object they are called on.
Even if the method is not mutating,
its result may be influenced by local state
(the implicit environment mentioned above)
that is not visible from the outside,
which makes method calls more difficult to reason about.

In functional languages, 
the results of functions are only determined by their arguments.
Functions can take data as arguments and return data as result,
but datatypes and functions are defined separately.
Data definitions are describing values without any behavior.
Functions can be defined in separate modules from the data they operate on.
In typed functional languages,
the values functions operate on are usually defined using algebraic datatypes,
and the functions operating on data are usually defined using pattern matching.

Over the years, Java has incorporated
[language features from functional programming](https://sebfisch.github.io/java-fun/)
that have changed how Java programmers write their code.
Recent versions of Java
include new language features from
[Project Amber](https://openjdk.java.net/projects/amber/)
inspired by algebraic datatypes and pattern matching 
that change the way data can be defined and used in Java.
This tutorial provides an overview and an in-depth exploration 
of related features that have been included
in [JDK Version 17](https://openjdk.java.net/projects/jdk/17/)
and [JDK Version 21](https://openjdk.java.net/projects/jdk/21/)

Before we look at specifics,
we discuss the underlying concepts algebraic datatypes and pattern matching
as a [background](docs/background)
to understand the motivation behind the new features.

The underlying source code is available online, 
and this tutorial includes tasks to extend it.
If you want to follow along, you can use your own Java development environment
or install
[Docker](https://docs.docker.com/get-docker/)
and
[VS Code](https://code.visualstudio.com/download)
with the
[Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)
to use a predefined environment without creating (or adjusting) your own.
To use the predefined environment in VS Code
download and unpack the
[source code](https://github.com/sebfisch/java-data-code/archive/main.zip)
(or clone the
[repository](https://github.com/sebfisch/java-data-code)
using `git`),
open the repository folder in VS Code,
click on the Remote Containers icon in the bottom-left corner,
and select *Reopen Folder in Container*.


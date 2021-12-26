---
title: "Algebraic Datatypes and Pattern Matching in Java"
---

# Algebraic Datatypes and Pattern Matching in Java

Over the years, Java has incorporated
[language features from functional programming](https://sebfisch.github.io/java-fun/)
that have changed how Java programmers write their code.
The long-term support (LTS) release of
[JDK Version 17](https://openjdk.java.net/projects/jdk/17/)
includes new language features from
[Project Amber](https://openjdk.java.net/projects/amber/)
inspired by functional languages that change the way data can be defined and used.
This tutorial provides an overview and an in-depth exploration of related features
that have been included since the previous LTS release.

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


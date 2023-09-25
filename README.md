# Algebraic Datatypes and Pattern Matching in Java

This repository contains the sources for the Tutorial:
[Algebraic Datatypes and Pattern Matching in Java](https://sebfisch.github.io/java-data/).

It provides a docker container that can be used to write and generate the corresponding site.

On first use you should clone submodules as follows.

    # git submodule init && git submodule update

Inside the container, you can start the development server using

    # hugo server --bind 0.0.0.0

The site should then be available at [http://localhost:1313/java-data/].

You can use your global git config in the container as follows.

    # cp ~/.gitconfig .git/config

Your ssh keys are mounted into the container automatically.
If that does not work or you have permission issues
you may want to adjust corresponding settings in the `docker-compose.yml` file.

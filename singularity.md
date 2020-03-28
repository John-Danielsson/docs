# Singularity Containers

## What is a Singularity Container?

This quote from the Wikipedia article on Singularity sums it up nicely.

_"One of the main uses of Singularity is to bring containers and reproducibility to scientific computing and the high-performance computing (HPC) world.[5] [...] Using Singularity containers, developers can work in reproducible environments of their choosing and design, and these complete environments can easily be copied and executed on other platforms.[7]"_

## Creating a Singularity Container
Some applications may have dependencies which require root access for installing. If these dependencies are not installed on mox.hyak or ikt.hyak, then you can use Singularity to run such applications.

[Singularity](https://sylabs.io/singularity/) allows you to package your application with all its dependencies into a single singularity image file ( .sif file).

If you know how to install your application on a Linux machine where you have root permissions, then you can prepare a Singularity .def file and request the Hyak team to prepare a Singularity container for your application.

See [this link](https://sylabs.io/guides/3.0/user-guide/definition_files.html) for more information on a Singularity .def file.

If you have root access to a Linux machine then you can [install Singularity CE](https://github.com/sylabs/singularity/releases) and use it to prepare a .sif image file.

If you have admin access to a Mac machine then you can install [Singularity Desktop macOS](https://sylabs.io/singularity-desktop-macos/) and use it to prepare a .sif image file.
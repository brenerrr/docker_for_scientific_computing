
# Docker for Scientific Computing: A Comprehensive Guide

Have you ever lost a bunch of time figuring out tons of dependencies to run your scientific computing application? Maybe you're trying to compile a code written 40 years ago that only works with a compiler version not available in your current OS (true story). After weeks, you finally make it work. You implement a super useful functionality and your peer asks if they can give it a go. Now they better really want to give it a go since they will have to go through all the hassle you went through to install everything required. Or maybe you simply have a new computer and need to install everything again.

Well, what if I told you that after setting things up just once, you could reliably always load this environment with all required dependencies with a single command and all you would need is a single software called [Docker](https://www.docker.com/)?


This repository contains a series of tutorials showing how to use Docker to easily create a software development environment **focused on scientific computing**. After going through all chapters, you will be able to launch an environment containing all dependencies you want, independent of your operating system's type and version, with a single command. You will be able to use Windows and execute code that uses OpenMPI and CUDA developed on Linux without having to install anything. It seems like magic, but it is just the power of containers.

Although not strictly necessary, I recommend going through all chapters in order.

1. [First Things First](src/1.%20First%20Things%20First/First%20Things%20First.md)
2. [Getting Started with Docker Containers](src/2.%20Getting%20Started%20with%20Docker%20Containers/Getting%20Started%20with%20Docker%20Containers.md)
3. [Volumes](src/3.%20Volumes/Volumes.md)
4. [Graphical Applications](src/4.%20Graphical%20Applications/Graphical%20Applications.md)
5. [MPI](src/5.%20MPI/MPI.md)
6. [CUDA](src/6.%20CUDA/CUDA.md)
7. [Creating Your Own Image](src/7.%20Creating%20Your%20Own%20Image/Creating%20Your%20Own%20Image.md)
8. [Integration with VS Code](src/8.%20Integration%20with%20VS%20Code/Integration%20with%20VS%20Code.md)
9. [Wrap it Up](src/9.%20Wrap%20It%20Up/Wrap%20It%20Up.md)



Comments and suggestions are welcome. If you have any feel free to open an issue.
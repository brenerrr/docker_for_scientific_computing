# CGNS
This Dockerfile provides two main packages:

- The compiled CFD General Notation System (CGNS) library for Fortran and C, including all optional packages and support for parallel HDF5.
- The PyCGNS package, which includes various tools and libraries for CGNS end-users and Python application developers. It is also compiled with all available submodules.

> [!WARNING]
> The PyCGNS library works only with CGNS files formatted using HDF5. To use it with files in the older ADF format, you must convert them first. This can be easily done using the command `cgnsconvert file.cgns` from a shell attached to the container.

To build a Docker image and container using this Dockerfile, follow these steps:

1. Download the Dockerfile, open a terminal in the same directory and run the command `docker build -t brenerrr/cgns . ` to build the image. Alternatively, you can directly download it with `docker image pull brenerrr/cgns`.
2. Before launching the container, a group with the current user as well as docker needs to be created. This will make sure the current user has permission to access files created inside the container. Execute the commands below to do so. The directory `/path/to/local/data` is the volume that will be shared between container and host (see [Volumes chapter](../../src/3.%20Volumes/Volumes.md)).
```
sudo groupadd -g 9999 dockeraccess
sudo usermod -aG dockeraccess $(whoami)
sudo chown -R :dockeraccess /path/to/local/data
sudo chmod -R 2770 /path/to/local/data
```
1. Once the image is built, you can start a container with: `docker run --rm -it --network host --env DISPLAY=$DISPLAY --volume "/path/to/local/data:/root/data" brenerrr/cgns`.



> [!IMPORTANT]
> If you encounter issues with graphical applications, refer to the [Graphical Applications chapter](../../src/4.%20Graphical%20Applications/Graphical%20Applications.md).

By default, the container launches a terminal. If you only want to run a specific executable (e.g., cgnsview), you can do so directly with `docker run --rm -it --network host --env DISPLAY=$DISPLAY --volume "/path/to/local/data:/root/data" brenerrr/cgns cgnsview `.


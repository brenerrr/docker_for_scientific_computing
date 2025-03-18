# Amber Molecular Dynamics Package

Amber is a suite of biomolecular simulation programs created in the late 1970's which is still in active development today. Although quite powerful, its installation can be difficult, making it a perfect example of how Docker can make things easier.

I have already prepared a Dockerfile which installs all Amber dependencies as well as packages needed for running it with MPI, CUDA and other additional functionalities. Just follow the steps below to create a container Amber-ready. See the [CUDA chapter](../6.%20CUDA/CUDA.md) to properly configure the host to run containers using CUDA.

> [!IMPORTANT]
> If you don't have an NVIDIA GPU, modify the line 123 of the Dockerfile to *-DCUDA=FALSE -DNCCL=FALSE DMVAPICH2GDR_GPU_DIRECT_COMM=FALSE* and comment the installation of CUDA and NCCL.

1. Create a folder called `amber_installation`
2. Download the [Dockerfile](./Dockerfile) and store it in `amber_installation`.
3. Download Amber24 and AmberTools24 from the [official Amber website](https://ambermd.org/GetAmber.php) and store them in `amber_installation`.
4. Execute `docker build -t amber_image .` in a terminal at `amber_installation`. This will take a long time.
5. Open a terminal at the folder you want to be visible in the container and execute  `docker run -it --name amber_container --gpus all --volume "${PWD}:/root/workdir" amber_image`. Ommit the `--gpus` flag if you aren't using CUDA.
6. Inside the container, set the necessary environment variabels with `source amber24/amber.sh`.
7. (Restart) If you want to use the container again start it with `docker start amber_container` and attach a terminal with `docker attach amber_container`.


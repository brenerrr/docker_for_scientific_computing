FROM ubuntu:20.04

# Use bash by default
SHELL ["/bin/bash", "-c"]

ENV LD_LIBRARY_PATH=/usr/local/lib
ENV OMPI_ALLOW_RUN_AS_ROOT=1
ENV OMPI_ALLOW_RUN_AS_ROOT_CONFIRM=1

# Basic functionalities
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive \
    apt-get install \
    cmake wget vim build-essential python3 jq git gcc gfortran -y

# Minimal python
RUN \
    apt-get update && apt-get install python-is-python3 python3-pip -y && \
    python3 -m pip install cython setuptools

# MPI
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install openmpi-bin\
    openmpi-common openssh-client openssh-server libopenmpi-dev -y

# CUDA
RUN \
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin && \
    mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600 && \
    wget https://developer.download.nvidia.com/compute/cuda/12.4.1/local_installers/cuda-repo-ubuntu2004-12-4-local_12.4.1-550.54.15-1_amd64.deb && \
    dpkg -i cuda-repo-ubuntu2004-12-4-local_12.4.1-550.54.15-1_amd64.deb && \
    cp /var/cuda-repo-ubuntu2004-12-4-local/cuda-*-keyring.gpg /usr/share/keyrings/ && \
    apt-get update && \
    apt-get -y install cuda-toolkit-12-4 && \
    rm cuda-repo-ubuntu2004-12-4-local_12.4.1-550.54.15-1_amd64.deb

# Lapack and Blas
RUN \
    wget https://github.com/Reference-LAPACK/lapack/archive/refs/tags/v3.12.1.tar.gz -O lapack.tar.gz && \
    tar -xzvf lapack.tar.gz && \
    mkdir lapack-build && \
    cd lapack-build && \
    cmake -DBUILD_SHARED_LIBS=ON ../lapack-3.12.1/ && \
    cmake --build . -j --target install && \
    cd .. && rm -rf lapack*

# Conda
RUN \
    mkdir -p ~/miniconda3 && \
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh && \
    bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3 && \
    rm ~/miniconda3/miniconda.sh &&  \
    source ~/miniconda3/bin/activate && \
    conda init --all

# Activate page up/down history search
RUN sed -i '41s/^..//' /etc/inputrc
RUN sed -i '42s/^..//' /etc/inputrc
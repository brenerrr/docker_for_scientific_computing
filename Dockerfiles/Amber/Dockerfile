FROM ubuntu:20.04

SHELL ["/bin/bash", "-c"]

ENV LD_LIBRARY_PATH=/usr/local/lib
ENV OMPI_ALLOW_RUN_AS_ROOT=1
ENV OMPI_ALLOW_RUN_AS_ROOT_CONFIRM=1

# Basic functionalities
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive \
    apt-get install \
    cmake \
    wget \
    vim \
    build-essential \
    python3 \
    jq \
    git \
    -y

# Minimal python
RUN \
    apt-get update && apt-get install python-is-python3 python3-pip -y && \
    python3 -m pip install cython setuptools


# Install requirements
RUN apt -y update && \
    apt -y install tcsh make \
    gcc gfortran \
    flex bison patch bc wget \
    xorg-dev libz-dev libbz2-dev \
    openmpi-bin libopenmpi-dev openssh-client

# Lapack and Blas
RUN \
    wget https://github.com/Reference-LAPACK/lapack/archive/refs/tags/v3.12.1.tar.gz -O lapack.tar.gz && \
    tar -xzvf lapack.tar.gz && \
    mkdir lapack-build && \
    cd lapack-build && \
    cmake -DBUILD_SHARED_LIBS=ON ../lapack-3.12.1/ && \
    cmake --build . -j --target install && \
    cd .. && rm -rf lapack*


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

# FFTW
RUN \
    wget https://fftw.org/pub/fftw/fftw-3.3.9.tar.gz -O fftw.tar.gz && \
    tar -xvzf fftw.tar.gz && \
    cd fftw-3.3.9/ && \
    ./configure --enable-shared --enable-mpi --enable-openmp --enable-threads && \
    make -j && \
    make -j install && \
    cd .. && \
    rm -rf fftw.tar.gz fftw-3.3.9/

# Arrayfire
RUN \
    wget https://arrayfire.s3.amazonaws.com/3.9.0/ArrayFire-v3.9.0_Linux_x86_64.sh -O arrayfire.sh  && \
    mkdir arrayfire && cp arrayfire.sh arrayfire && cd arrayfire && \
    bash arrayfire.sh --skip-license && \
    cp -r include/* /usr/include/ && \
    cp lib64/* /usr/lib/x86_64-linux-gnu/ && \
    cp lib64/* /usr/local/lib && \
    cd .. && rm -rf arrayfire*

# GSL
RUN \
    wget https://gnu.c3sl.ufpr.br/ftp/gsl/gsl-2.8.tar.gz -O gsl.tar.gz && \
    tar -xvf gsl.tar.gz  && \
    cd gsl-2.8  && \
    ./configure  && \
    make -j && \
    make -j install  && \
    cd ..  && rm -rf gsl*


# Plumed
RUN \
    wget https://github.com/plumed/plumed2/releases/download/v2.9.2/plumed-2.9.2.tgz -O plumed.tgz && \
    tar -xvf plumed.tgz && \
    cd plumed-2.9.2 && \
    ./configure && \
    make  && \
    make install && \
    cd .. && rm -rf plumed*

# NCCL
RUN \
    git clone https://github.com/NVIDIA/nccl && \
    cd nccl && \
    git checkout 80f6bda4378b99d99e82b4d76a633791cc45fef0 && \
    make src.build && \
    make install && \
    cd .. && \
    rm -rf nccl*


# Amber
WORKDIR "/root"
COPY Amber24.tar.bz2 AmberTools24.tar.bz2 /root/
RUN \
    tar xvfj AmberTools24.tar.bz2 && \
    tar xvfj Amber24.tar.bz2 && \
    cd amber24_src/ && \
    python3 update_amber --update

# Adjust run_cmake
RUN \
    cd amber24_src/build && \
    sed -i '42d' run_cmake && \
    sed -i '42i -DMPI=TRUE -DCUDA=TRUE -DINSTALL_TESTS=TRUE -DNCCL=TRUE -DOPENMP=TRUE -DMVAPICH2GDR_GPU_DIRECT_COMM=TRUE \\' run_cmake && \
    ./run_cmake && \
    make install && \
    cd ../../ && rm -r amber24_src Amber*.bz2

FROM ubuntu:20.04

WORKDIR "/root"

SHELL ["/bin/bash", "-c"]

# Basic functionalities
ENV LD_LIBRARY_PATH=/usr/local/lib
ENV OMPI_ALLOW_RUN_AS_ROOT=1
ENV OMPI_ALLOW_RUN_AS_ROOT_CONFIRM=1
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
    openmpi-bin libopenmpi-dev openssh-client \
    -y

# Activate page up/down history search
RUN sed -i '41s/^..//' /etc/inputrc
RUN sed -i '42s/^..//' /etc/inputrc

# Install python with conda
RUN \
    mkdir -p miniconda3 && \
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda3/miniconda.sh && \
    bash miniconda3/miniconda.sh -b -u -p ~/miniconda3 && \
    rm miniconda3/miniconda.sh

RUN \
    source ~/miniconda3/bin/activate && \
    conda init --all && \
    conda create -n env python=3.10 && \
    echo "conda activate env" >> ~/.bashrc


# Install requirements for pycgns
RUN \
    apt-get update && \
    apt install  build-essential qt5-default\
    ninja-build libgl1-mesa-dev pkg-config -y

RUN \
    source ~/miniconda3/bin/activate && \
    conda activate env && \
    pip install numpy cython PyQt5 PyQt6 meson meson-python QtPy && \
    conda install -y conda-forge::vtk  && \
    conda install -y conda-forge::qt


# Substitute for SZIP (required by HDF5)
RUN\
    wget https://github.com/MathisRosenhauer/libaec/releases/download/v1.1.3/libaec-1.1.3.tar.gz && \
    tar -xf libaec-1.1.3.tar.gz && \
    cd libaec-1.1.3 && \
    ./configure  && \
    make  && \
    make install && \
    rm -rf /root/libaec-1.1.3.tar.gz /root/libaec-1.1.3

# HDF5
ENV C_INCLUDE_PATH=/usr/lib/x86_64-linux-gnu/openmpi/include
RUN \
    wget https://github.com/HDFGroup/hdf5/archive/refs/tags/hdf5_1.14.6.tar.gz && \
    tar -xf hdf5_1.14.6.tar.gz && \
    cd hdf5-hdf5_1.14.6/ && \
    ./configure --enable-parallel --prefix=/root/HDF5 && \
    make  && \
    make install  && \
    rm -rf /root/hdf5_1.14.6.tar.gz /root/hdf5-hdf5_1.14.6

ENV PATH=${PATH}:/root/HDF5/bin
ENV LD_LIBRARY_PATH=/root/HDF5/lib:${LD_LIBRARY_PATH}
ENV C_INCLUDE_PATH=/root/HDF5/include:${C_INCLUDE_PATH}

# CGNS
# If you are using a gfortran version >= 10 make sure to add the flag -fallow-argument-mismatch below
ENV FLIBS="-Wl,--no-as-needed -lmpi -ldl -lz -lm -lpthread"
ENV LIBS="-Wl,--no-as-needed -lmpi -ldl -lz -lm -lpthread"
# Necessary for cgnstools
ENV CPATH=/usr/include/tk
ENV FC=/usr/bin/gfortran
RUN \
    mkdir CGNS_src && \
    cd CGNS_src && \
    wget https://github.com/CGNS/CGNS/archive/refs/tags/v4.4.0.tar.gz && \
    tar -xvf v4.4.0.tar.gz && \
    cd CGNS-4.4.0 && \
    apt-get update && \
    apt-get install expect tcl-dev tk-dev gfortran libxmu-dev -y && \
    cd src &&\
    mkdir $HOME/CGNS && \
    ./configure \
    --prefix=$HOME/CGNS \
    --with-fortran \
    --enable-lfs \
    --enable-64bit \
    --disable-shared \
    --enable-debug \
    --enable-cgnstools \
    --with-hdf5=/root/HDF5 \
    --enable-parallel \
    --with-szip \
    --enable-64bit && \
    make && \
    make install && \
    rm -rf /root/CGNS_src

ENV C_INCLUDE_PATH=/root/CGNS/include:${C_INCLUDE_PATH}
ENV LD_LIBRARY_PATH=/root/CGNS/lib:${LD_LIBRARY_PATH}
ENV PATH=/root/CGNS/bin:/root/CGNS/bin/cgnstools:${PATH}

# Pycgns
RUN \
    wget https://github.com/pyCGNS/pyCGNS/archive/refs/tags/v6.3.1.tar.gz && \
    tar -xf v6.3.1.tar.gz && \
    cd pyCGNS-6.3.1 && \
    source ~/miniconda3/bin/activate && \
    conda activate env && \
    sed -i "10 c\option(\'app\', type: \'boolean\', value: true," meson_options.txt && \
    sed -i "12 c\option(\'nav\', type: \'boolean\', value: true," meson_options.txt && \
    pip install . --no-build-isolation

USER 9999:9999
RUN "umask 002" >> ~/.bashrc

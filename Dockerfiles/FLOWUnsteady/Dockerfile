FROM julia:1.11

WORKDIR "/home"

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

# Install python with conda
RUN \
    mkdir -p miniconda3 && \
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda3/miniconda.sh && \
    bash miniconda3/miniconda.sh -b -u -p ~/miniconda3 && \
    rm miniconda3/miniconda.sh

SHELL ["/bin/bash", "-c"]
RUN \
    source ~/miniconda3/bin/activate && \
    conda init --all && \
    conda create -n env python=3.10 && \
    echo "conda activate env" >> ~/.bashrc


# Install FLOWVPM
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install cmake g++ mpich -y
RUN julia -e 'using Pkg; Pkg.add(name="CxxWrap", version="0.15.0")' && \
    git clone https://github.com/byuflowlab/FLOWExaFMM FLOWExaFMM && \
    cd FLOWExaFMM && \
    sh build.sh
RUN curl -fsSL https://github.com/fredrikekre/jlpkg/releases/download/v1.6.0/jlpkg-v1.6.0.tar.gz | \
    tar -xzC /usr/bin && \
    jlpkg develop /home/FLOWExaFMM && \
    jlpkg add https://github.com/byuflowlab/FLOWVPM.jl
RUN \
    source ~/miniconda3/bin/activate && \
    conda init --all && \
    conda activate env && \
    pip install numpy mpmath scipy matplotlib && \
    conda run -n env julia -e 'using Pkg; Pkg.add( Pkg.PackageSpec(; url="https://github.com/byuflowlab/AirfoilPrep.jl", rev="v2.1.2") )' && \
    conda run -n env julia -e 'using Pkg; Pkg.add( Pkg.PackageSpec(; url="https://github.com/byuflowlab/FLOWNoise", rev="v2.3.3") )' && \
    conda run -n env julia -e 'using Pkg; Pkg.add( Pkg.PackageSpec(; url="https://github.com/byuflowlab/FLOWVLM", rev="v2.1.3") )' && \
    conda run -n env julia -e 'using Pkg; Pkg.add( Pkg.PackageSpec(; url="https://github.com/byuflowlab/BPM.jl", rev="v2.0.1") )' && \
    conda run -n env jlpkg add https://github.com/byuflowlab/FLOWUnsteady

RUN sed -i '41s/^..//' /etc/inputrc
RUN sed -i '42s/^..//' /etc/inputrc

CMD ["/bin/bash"]

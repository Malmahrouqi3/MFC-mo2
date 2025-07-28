ARG BASE_IMAGE=ubuntu:22.04
ARG BENCH=false
FROM ${BASE_IMAGE}

RUN apt-get update -y && \
    apt-get install -y \
        build-essential git make cmake gcc g++ gfortran \
        python3 python3-venv python3-pip \
        openmpi-bin libopenmpi-dev libfftw3-dev \
        libatlas-base-dev mpich libmpich-dev && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENV OMPI_ALLOW_RUN_AS_ROOT=1
ENV OMPI_ALLOW_RUN_AS_ROOT_CONFIRM=1
ENV PATH="/opt/MFC:$PATH"
ENV MAKEFLAGS="-j1"
ENV CMAKE_BUILD_PARALLEL_LEVEL=1

COPY ../ /opt/MFC

RUN curl https://developer.download.nvidia.com/hpc-sdk/ubuntu/DEB-GPG-KEY-NVIDIA-HPC-SDK | gpg --dearmor -o /usr/share/keyrings/nvidia-hpcsdk-archive-keyring.gpg && \
    echo 'deb [signed-by=/usr/share/keyrings/nvidia-hpcsdk-archive-keyring.gpg] https://developer.download.nvidia.com/hpc-sdk/ubuntu/amd64 /' | tee /etc/apt/sources.list.d/nvhpc.list && \
    apt-get update -y && \
    apt-get install -y nvhpc-25-7-cuda-multi

ARG CC_COMPILER=nvc
ARG CXX_COMPILER=nvc++
ARG FC_COMPILER=nvfortran
ARG COMPILER_PATH=/opt/nvidia/hpc_sdk/Linux_x86_64/compilers/bin
ARG COMPILER_LD_LIBRARY_PATH=/opt/nvidia/hpc_sdk/Linux_x86_64/compilers/lib

ENV CC=${CC_COMPILER}
ENV CXX=${CXX_COMPILER}
ENV FC=${FC_COMPILER}
ENV PATH="${COMPILER_PATH}:$PATH"
ENV LD_LIBRARY_PATH="${COMPILER_LD_LIBRARY_PATH}:$LD_LIBRARY_PATH"

RUN free -h && df -h
RUN cd /opt/MFC && rm -rf build
RUN free -h && df -h
RUN cd /opt/MFC && \
    rm -rf build && \
    ./mfc.sh build --mpi --gpu -j 1 && \
    ./mfc.sh test --dry-run --gpu

RUN rm -rf /var/lib/apt/lists/*
RUN free -h && df -h

WORKDIR /opt/MFC
ENTRYPOINT ["/bin/bash"]
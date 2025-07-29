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

ARG CC_COMPILER=gcc
ARG CXX_COMPILER=g++
ARG FC_COMPILER=gfortran
ARG COMPILER_PATH=/usr/bin
ARG COMPILER_LD_LIBRARY_PATH=/usr/lib

ENV CC=${CC_COMPILER}
ENV CXX=${CXX_COMPILER}
ENV FC=${FC_COMPILER}
ENV PATH="${COMPILER_PATH}:$PATH"
ENV LD_LIBRARY_PATH="${COMPILER_LD_LIBRARY_PATH}:$LD_LIBRARY_PATH"

RUN free -h && df -h
RUN cd /opt/MFC && rm -rf build
RUN free -h && df -h
RUN cd /opt/MFC && \
    if echo "$BASE_IMAGE" | grep -q "nvidia"; then \
      ./mfc.sh build --mpi --gpu -j 1; \
    else \
      ./mfc.sh build -j 1; \
    fi
RUN free -h && df -h
RUN cd /opt/MFC && \
    if echo "$BASE_IMAGE" | grep -q "nvidia"; then \
      ./mfc.sh test --dry-run --gpu; \
    else \
      ./mfc.sh test --dry-run; \
    fi

RUN rm -rf /var/lib/apt/lists/*
RUN free -h && df -h

WORKDIR /opt/MFC
ENTRYPOINT ["/bin/bash"]
ARG BASE_IMAGE=ubuntu:22.04
ARG BENCH=false
FROM ${BASE_IMAGE}

RUN apt-get update -y && \
    apt-get install -y \
        build-essential git make cmake \
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

ENV CC=nvc
ENV CXX=nvc++
ENV FC=nvfortran
ENV PATH="/opt/nvidia/hpc_sdk/Linux_x86_64/compilers/bin:$PATH"
ENV LD_LIBRARY_PATH="/opt/nvidia/hpc_sdk/Linux_x86_64/compilers/lib:$LD_LIBRARY_PATH"

# ARG BENCH
# RUN if [ "$BENCH" = "true" ] && ! echo "$BASE_IMAGE" | grep -q "nvidia"; then \
#       apt-get install -y --no-install-recommends libatlas-base-dev mpich libmpich-dev gfortran && \
#       wget https://www.netlib.org/benchmark/hpl/hpl-2.3.tar.gz && \
#       tar -xzvf hpl-2.3.tar.gz && \
#       mv hpl-2.3 hpl; \
#     fi

# RUN if [ "$BENCH" = "true" ] && echo "$BASE_IMAGE" | grep -q "nvidia"; then \
#       wget https://developer.download.nvidia.com/compute/nvidia-hpc-benchmarks/25.04/local_installers/nvidia-hpc-benchmarks-local-repo-ubuntu2404-25.04_1.0-1_amd64.deb -O /tmp/nvidia-hpc-benchmarks.deb && \
#       dpkg -i /tmp/nvidia-hpc-benchmarks.deb && \
#       cp /var/nvidia-hpc-benchmarks-local-repo-ubuntu2404-25.04/nvidia-hpc-benchmarks-*-keyring.gpg /usr/share/keyrings/; \
#     fi
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
RUN free -h && df -h
WORKDIR /opt/MFC
ENTRYPOINT ["/bin/bash"]
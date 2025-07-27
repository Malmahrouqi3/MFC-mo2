ARG BASE_IMAGE=ubuntu:22.04
ARG BENCH=false
FROM ${BASE_IMAGE}

COPY ../ /opt/MFC

ENV OMPI_ALLOW_RUN_AS_ROOT=1
ENV OMPI_ALLOW_RUN_AS_ROOT_CONFIRM=1
ENV PATH="/opt/MFC:$PATH"

RUN apt-get update -y && \
    apt-get install -y \
        build-essential git make cmake gcc g++ \
        python3 python3-venv python3-pip \
        openmpi-bin libopenmpi-dev libfftw3-dev \
        libatlas-base-dev mpich libmpich-dev gfortran

ARG BASE_IMAGE
RUN if echo "$BASE_IMAGE" | grep -q "nvidia"; then \
      export CC=nvc && \
      export CXX=nvc++ && \
      export FC=nvfortran && \
      export PATH="/opt/nvidia/hpc_sdk/Linux_x86_64/compilers/bin:$PATH" && \
      export LD_LIBRARY_PATH="/opt/nvidia/hpc_sdk/Linux_x86_64/compilers/lib:$LD_LIBRARY_PATH"; \
    fi

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

RUN cd /opt/MFC && rm -rf build
RUN cd /opt/MFC && \
    if echo "$BASE_IMAGE" | grep -q "nvidia"; then \
      ./mfc.sh build --mpi --gpu -j 2; \
    else \
      ./mfc.sh build -j 2; \
    fi
RUN cd /opt/MFC && \
    if echo "$BASE_IMAGE" | grep -q "nvidia"; then \
      ./mfc.sh test --dry-run --gpu -j 2; \
    else \
      ./mfc.sh test --dry-run -j 2; \
    fi
    
WORKDIR /opt/MFC
ENTRYPOINT ["/bin/bash"]
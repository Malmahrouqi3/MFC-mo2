ARG BASE_IMAGE
FROM ${BASE_IMAGE}

ARG TARGET
ARG INTERFACE
ARG CC_COMPILER
ARG CXX_COMPILER
ARG FC_COMPILER
ARG COMPILER_PATH
ARG COMPILER_LD_LIBRARY_PATH


RUN apt-get update -y && apt-get install -y wget tar xz-utils build-essential gcc libssl-dev && \
  wget https://www.python.org/ftp/python/3.14.0/Python-3.14.0.tar.xz && \
  tar -xf Python-3.14.0.tar.xz && cd Python-3.14.0 && \
  ./configure --enable-optimizations && make -s -j $(nproc) && make install && \
  if [ "$TARGET" != "gpu" ]; then \
    apt-get install -y \
      build-essential curl git make cmake g++ gfortran bc\
      openmpi-bin libopenmpi-dev libfftw3-dev \
      mpich libmpich-dev; \
  else \
    apt-get install -y \
      build-essential curl git make cmake bc\
      libfftw3-dev \
      openmpi-bin libopenmpi-dev; \
  fi && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENV OMPI_ALLOW_RUN_AS_ROOT=1
ENV OMPI_ALLOW_RUN_AS_ROOT_CONFIRM=1
ENV PATH="/opt/MFC:$PATH"

COPY ../ /opt/MFC

ENV INTERFACE=${INTERFACE}
ENV CC=${CC_COMPILER}
ENV CXX=${CXX_COMPILER}
ENV FC=${FC_COMPILER}
ENV PATH="${COMPILER_PATH}:$PATH"
ENV LD_LIBRARY_PATH="${COMPILER_LD_LIBRARY_PATH}:${LD_LIBRARY_PATH:-}"

RUN echo "TARGET=$TARGET CC=$CC_COMPILER FC=$FC_COMPILER" && \
    cd /opt/MFC && \
    if [ "$TARGET" = "gpu" ]; then \
      ./mfc.sh build --gpu $INTERFACE -j $(nproc); \
    else \
      ./mfc.sh build -j $(nproc); \
    fi

RUN cd /opt/MFC && \
    if [ "$TARGET" = "gpu" ]; then \
      ./mfc.sh test -a --dry-run --gpu $INTERFACE -j $(nproc); \
    else \
      ./mfc.sh test -a --dry-run -j $(nproc); \
    fi

WORKDIR /opt/MFC
ENTRYPOINT ["tail", "-f", "/dev/null"]
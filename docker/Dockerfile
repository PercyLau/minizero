# Stage 1: Build Environment - 負責所有編譯和較重的安裝
FROM nvidia/cuda:12.4.1-cudnn-devel-ubuntu22.04

ENV TZ=Asia/Shanghai

RUN ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime && \
    echo ${TZ} > /etc/timezone

ARG DEBIAN_FRONTEND=noninteractive


RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install python3-dev python3-pip python-is-python3 -y

RUN apt-get install -y --no-install-recommends \
    vim \
    gdb \
    git \
    less \
    htop \
    wget \
    cmake \
    ffmpeg \
    clang-format \
    python3-opencv \
    python3-pybind11 \
    libopencv-dev \
    libarchive-tools \
    libboost-all-dev \
    unzip \
    gconf2 \
    default-jre && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# 安裝 Python 包，並在同一層清理 pip 緩存
RUN python -m pip install \
    numpy \
    pandas \
    jupyter \
    matplotlib && \
    pip cache purge

RUN pip install torch==2.5.1+cu124 --index-url https://download.pytorch.org/whl/cu124
COPY libtorch_zip_part_*/tmp/ 
RUN cat /tmp/libtorch_zip_part_* > /tmp/libtorch-cxx11-abi-shared-with-deps-2.5.1+cu124-minizero.zip &&\
    unzip /tmp/libtorch-cxx11-abi-shared-with-deps-2.5.1+cu124-minizero.zip -d /usr/local
ENV CMAKE_PREFIX_PATH=/usr/local/libtorch/share/cmake/Torch

RUN python3 -m pip install gymnasium[atari] && \
    python3 -m pip cache purge

RUN mkdir -p /tmp/ale_build && \
    git clone https://github.com/mgbellemare/Arcade-Learning-Environment.git /tmp/ale_build/Arcade-Learning-Environment && \
    cd /tmp/ale_build/Arcade-Learning-Environment && \
    mkdir build && cd build && \
    cmake ../ -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_STANDARD=17 -DBUILD_PYTHON_LIB=OFF && \
    cmake --build . --target install -j && \
    rm -rf /tmp/ale_build

RUN git clone -b v1.6.0 https://github.com/Remi-Coulom/gogui.git /tmp/gogui_repo && \
    echo "exit 0" >> /tmp/gogui_repo/install.sh && \
    cd /tmp/gogui_repo && \
    ./install.sh && \
    cd / && \
    rm -rf /tmp/gogui_repo

RUN ldconfig && rm -rf /tmp/*

# 創建工作目錄
RUN mkdir /workspace
WORKDIR /workspace
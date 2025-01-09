# Base image with Ubuntu 22.04
FROM nvidia/cuda:12.2.0-devel-ubuntu22.04
ARG DEBIAN_FRONTEND=noninteractiv


RUN apt-get update && apt-get install -y \
    curl \
    less \
    software-properties-common


# # Set environment variables for Vulkan
ENV NVIDIA_DRIVER_CAPABILITIES=compute,graphics,utility \
    PATH="/usr/local/cuda/bin:${PATH}" \
    LD_LIBRARY_PATH="/usr/local/cuda/lib64:${LD_LIBRARY_PATH}"

# # Configure runtime to use NVIDIA Container Toolkit
LABEL com.nvidia.volumes.needed="nvidia_driver"

RUN apt install wget
RUN wget -qO- https://packages.lunarg.com/lunarg-signing-key-pub.asc | tee /etc/apt/trusted.gpg.d/lunarg.asc \
    && wget -qO /etc/apt/sources.list.d/lunarg-vulkan-jammy.list https://packages.lunarg.com/vulkan/lunarg-vulkan-jammy.list \
    && apt update 

RUN  apt install -y vulkan-sdk

RUN apt-get update && apt-get install -y \
    build-essential \
    clang \
    cmake \
    curl \ 
    git \
    && rm -rf /var/lib/apt/lists/*

ARG REINSTALL_CMAKE_VERSION_FROM_SOURCE="3.22.2"

# Optionally install the cmake for vcpkg
COPY ./reinstall-cmake.sh /tmp/

RUN update-alternatives --install /usr/bin/clang clang /usr/bin/clang-14 100 \
    && update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-14 100 \
    && update-alternatives --install /usr/bin/cc cc /usr/bin/clang 100 \
    && update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++ 100 

RUN if [ "${REINSTALL_CMAKE_VERSION_FROM_SOURCE}" != "none" ]; then \
        chmod +x /tmp/reinstall-cmake.sh && /tmp/reinstall-cmake.sh ${REINSTALL_CMAKE_VERSION_FROM_SOURCE}; \
    fi \
    && rm -f /tmp/reinstall-cmake.sh

RUN apt update && apt install -y \
    libglvnd0 libgl1 libglx0 libegl1 libgles2 libsdl2-dev

RUN apt update && apt install -y \
     libc++-14-dev \
     libc++abi-14-dev \
     ninja-build  \
     file

ENV VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/nvidia_icd.json

FROM ubuntu:24.04

ARG ZEPHYR_SDK_VERSION=0.16.9

ENV DEBIAN_FRONTEND=noninteractive \
    PATH="/opt/venv/bin:${PATH}" \
    ZEPHYR_SDK_INSTALL_DIR="/opt/zephyr-sdk-${ZEPHYR_SDK_VERSION}"

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      git cmake ninja-build gperf dfu-util device-tree-compiler wget \
      python3-dev python3-pip python3-setuptools python3-tk python3-wheel python3-venv \
      xz-utils file make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1 ca-certificates && \
    rm -rf /var/lib/apt/lists/*

RUN python3 -m venv /opt/venv && \
    pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir west

RUN SDK_ARCHIVE="zephyr-sdk-${ZEPHYR_SDK_VERSION}_linux-x86_64_minimal.tar.xz" && \
    wget -q \
      "https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v${ZEPHYR_SDK_VERSION}/${SDK_ARCHIVE}" \
      -O "/tmp/${SDK_ARCHIVE}" && \
    tar xf "/tmp/${SDK_ARCHIVE}" -C /opt && \
    "/opt/zephyr-sdk-${ZEPHYR_SDK_VERSION}/setup.sh" -c -t arm-zephyr-eabi && \
    rm -f "/tmp/${SDK_ARCHIVE}"

WORKDIR /workspace

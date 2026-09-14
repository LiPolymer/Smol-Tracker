FROM ubuntu:24.04 AS builder

ARG ZEPHYR_SDK_VERSION=0.16.9
ARG ZEPHYR_REVISION=ncs-v3.1.1

ENV DEBIAN_FRONTEND=noninteractive \
    PATH="/opt/venv/bin:${PATH}" \
    ZEPHYR_SDK_INSTALL_DIR="/opt/zephyr-sdk-${ZEPHYR_SDK_VERSION}"

# Build-only packages stay in this stage. This keeps compilers, headers,
# download tools and other one-time setup dependencies out of the runtime image.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      git cmake wget ca-certificates xz-utils \
      python3-dev python3-pip python3-setuptools python3-wheel python3-venv \
      gcc g++ make && \
    rm -rf /var/lib/apt/lists/*

RUN python3 -m venv /opt/venv && \
    pip install --no-cache-dir --upgrade pip

# Bake the exact Zephyr Python environment used by NCS 3.1.1 into the image.
# The temporary Zephyr clone is discarded afterwards; only the venv remains.
RUN git clone --quiet --depth=1 --branch "${ZEPHYR_REVISION}" \
      https://github.com/nrfconnect/sdk-zephyr.git /tmp/zephyr && \
    pip install --no-cache-dir -r /tmp/zephyr/scripts/requirements.txt && \
    rm -rf /tmp/zephyr

RUN SDK_ARCHIVE="zephyr-sdk-${ZEPHYR_SDK_VERSION}_linux-x86_64_minimal.tar.xz" && \
    wget -q \
      "https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v${ZEPHYR_SDK_VERSION}/${SDK_ARCHIVE}" \
      -O "/tmp/${SDK_ARCHIVE}" && \
    tar xf "/tmp/${SDK_ARCHIVE}" -C /opt && \
    "/opt/zephyr-sdk-${ZEPHYR_SDK_VERSION}/setup.sh" -c -t arm-zephyr-eabi && \
    rm -f "/tmp/${SDK_ARCHIVE}"


FROM ubuntu:24.04

ARG ZEPHYR_SDK_VERSION=0.16.9

ENV DEBIAN_FRONTEND=noninteractive \
    PATH="/opt/venv/bin:${PATH}" \
    ZEPHYR_SDK_INSTALL_DIR="/opt/zephyr-sdk-${ZEPHYR_SDK_VERSION}"

# Runtime-only build prerequisites for this nRF52840 target. The ARM compiler
# itself comes from the Zephyr SDK copied below.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      git cmake ninja-build gperf device-tree-compiler \
      python3 python3-venv file make libmagic1 ca-certificates && \
    rm -rf /var/lib/apt/lists/*

COPY --from=builder /opt/venv /opt/venv
COPY --from=builder /opt/zephyr-sdk-${ZEPHYR_SDK_VERSION} /opt/zephyr-sdk-${ZEPHYR_SDK_VERSION}

WORKDIR /workspace

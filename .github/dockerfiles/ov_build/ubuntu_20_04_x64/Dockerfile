ARG REGISTRY="docker.io"
FROM ${REGISTRY}/library/ubuntu:20.04

USER root

# APT configuration
RUN echo 'Acquire::Retries "10";' > /etc/apt/apt.conf && \
    echo 'APT::Get::Assume-Yes "true";' >> /etc/apt/apt.conf && \
    echo 'APT::Get::Fix-Broken "true";' >> /etc/apt/apt.conf && \
    echo 'APT::Get::no-install-recommends "true";' >> /etc/apt/apt.conf

ENV DEBIAN_FRONTEND="noninteractive" \
    TZ="Europe/London"

RUN apt-get update && \
    apt-get install software-properties-common && \
    add-apt-repository --yes --no-update ppa:git-core/ppa && \
    add-apt-repository --yes --no-update ppa:deadsnakes/ppa && \
    apt-get update && \
    apt-get install \
        curl \
        git \
        gpg-agent \
        tzdata \
        # parallel gzip
        pigz \
        # Pythons
        python3.9-dev \
        python3.9-venv \
        python3.9-distutils \
        python3.10-dev \
        python3.10-venv \
        python3.10-distutils \
        python3.11-dev \
        python3.11-venv \
        python3.11-distutils \
        python3.12-dev \
        python3.12-venv \
        python3.13-dev \
        python3.13-venv \
        # For Java API
        default-jdk \
        && \
    rm -rf /var/lib/apt/lists/*

# Install build dependencies
ADD install_build_dependencies.sh /install_build_dependencies.sh
RUN chmod +x /install_build_dependencies.sh && \
    bash -e /install_build_dependencies.sh && \
    rm -rf /var/lib/apt/lists/*

# Install sscache
ARG SCCACHE_VERSION="v0.7.5"
ENV SCCACHE_HOME="/opt/sccache" \
    SCCACHE_PATH="/opt/sccache/sccache"

RUN mkdir ${SCCACHE_HOME} && cd ${SCCACHE_HOME} && \
    SCCACHE_ARCHIVE="sccache-${SCCACHE_VERSION}-x86_64-unknown-linux-musl.tar.gz" && \
    curl -SLO https://github.com/mozilla/sccache/releases/download/${SCCACHE_VERSION}/${SCCACHE_ARCHIVE} && \
    tar -xzf ${SCCACHE_ARCHIVE} --strip-components=1 && rm ${SCCACHE_ARCHIVE}

ENV PATH="$SCCACHE_HOME:$PATH"

# Setup pip
ENV PIP_VERSION="24.0"
RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py && \
    curl https://bootstrap.pypa.io/pip/3.8/get-pip.py -o get-pip-3.8.py && \
    python3 get-pip-3.8.py --no-cache-dir pip==${PIP_VERSION} && \
    python3.9 get-pip.py --no-cache-dir pip==${PIP_VERSION} && \
    python3.10 get-pip.py --no-cache-dir pip==${PIP_VERSION} && \
    python3.11 get-pip.py --no-cache-dir pip==${PIP_VERSION} && \
    python3.12 get-pip.py --no-cache-dir pip==${PIP_VERSION} && \
    python3.13 get-pip.py --no-cache-dir pip==${PIP_VERSION} && \
    rm -f get-pip.py

# Use Python 3.9 as default instead of Python 3.8
# Using venv here 'cause other methods to switch the default Python on Ubuntu 20 break both system and wheels build
RUN python3.9 -m venv venv
ENV PATH="/venv/bin:$SCCACHE_HOME:$PATH"

ENV PIP_CACHE_DIR=/mount/caches/pip/linux/${PIP_VERSION}

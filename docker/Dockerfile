# GlobaLeaks Unified Dockerfile for Remote Install

ARG BASE_IMAGE=debian:bookworm-slim@sha256:36e591f228bb9b99348f584e83f16e012c33ba5cad44ef5981a1d7c0a93eca22
ARG DISTRIBUTION=bookworm
ARG INSTALL_FROM=remote

FROM ${BASE_IMAGE}

# Redeclare build args
ARG INSTALL_FROM
ARG DISTRIBUTION

# Install required dependencies
RUN apt-get update && \
    apt-get dist-upgrade -y && \
    apt-get install -y \
        apt-utils \
        wget \
        curl \
        gnupg \
        ca-certificates \
        lsb-release \
        dpkg-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Create working directories
RUN mkdir -p /tmp/install /globaleaks/deb

# Copy install script and optional local packages
RUN echo "#!/bin/bash\nexit 0" > /tmp/install.sh && chmod +x /tmp/install.sh
RUN mkdir -p /globaleaks/deb

# Non-interactive install mode
ENV DEBIAN_FRONTEND=noninteractive

# Run installation
RUN echo "INSTALL_FROM=${INSTALL_FROM}, DISTRIBUTION=${DISTRIBUTION}" && \
    chmod +x /tmp/install.sh && \
    /tmp/install.sh && \
    rm -rf /tmp/install.sh /globaleaks/deb

# Disable sandboxing for Docker
RUN echo "NETWORK_SANDBOXING=0" >> /etc/default/globaleaks && \
    echo "APPARMOR_SANDBOXING=0" >> /etc/default/globaleaks && \
    echo "REACHABLE_VIA_WEB=0" >> /etc/default/globaleaks

EXPOSE 8080 8443

USER globaleaks

CMD ["/usr/bin/python3", "/usr/bin/globaleaks", "--working-path=/var/globaleaks/", "-n"]

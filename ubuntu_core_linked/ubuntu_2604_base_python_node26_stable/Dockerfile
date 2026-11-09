FROM ghcr.io/dbca-wa/docker-apps-dev:ubuntu_2604_base_python AS builder_base_ubuntu2604_python

LABEL maintainer="asi@dbca.wa.gov.au"

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Australia/Perth
ENV NODE_VERSION=26.13.0

RUN apt-get clean && apt-get update && apt-get upgrade -y

# Install nodejs 26 (downloads with xz-utils, extracts, and purges xz-utils in one layer)
RUN apt-get update \
    && apt-get install -y --no-install-recommends xz-utils \
    && update-ca-certificates \
    && wget -q "https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-x64.tar.xz" -O /tmp/node.tar.xz \
    && tar -xf /tmp/node.tar.xz --directory /usr/local --strip-components 1 \
    && rm -f /tmp/node.tar.xz \
    && apt-get purge -y --auto-remove xz-utils \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Patch ONLY the 2 packages flagged in the Trivy scan for high/critical (Pinned to fixed versions)
RUN cd /tmp \
    && npm pack brace-expansion@5.0.12 \
    && tar -xzf brace-expansion-5.0.12.tgz \
    && rm -rf /usr/local/lib/node_modules/npm/node_modules/brace-expansion \
    && cp -a package /usr/local/lib/node_modules/npm/node_modules/brace-expansion \
    && rm -rf /tmp/*

RUN cd /tmp \
    && npm pack undici@6.29.1 \
    && tar -xzf undici-6.29.1.tgz \
    && rm -rf /usr/local/lib/node_modules/npm/node_modules/undici \
    && cp -a package /usr/local/lib/node_modules/npm/node_modules/undici \
    && rm -rf /tmp/*

# Smoke test: Ensures npm install works and dependency tree is unbroken
RUN cd /tmp && npm install --dry-run lodash

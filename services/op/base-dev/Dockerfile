FROM --platform=linux/amd64 ubuntu:22.04

# Update package lists and install necessary dependencies
RUN apt-get update && apt-get install -y \
    git \
    make \
    wget \
    gcc \
    jq \
    libusb-1.0-0-dev \
    libc6 \
    curl

# Install Node.js 16.x
RUN curl -fsSL https://deb.nodesource.com/setup_16.x | bash -
RUN apt-get install -y nodejs

# Install Yarn
RUN npm install --global yarn

# Install PNPM
RUN npm install --global pnpm@

# Set up Go environment variables
ENV GOROOT /usr/local/go
ENV GOPATH /go
ENV PATH $GOPATH/bin:$GOROOT/bin:$PATH

# Define the Go version to install
ARG GO_VERSION=1.20

# Download and install Go
RUN wget -q https://dl.google.com/go/go${GO_VERSION}.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf go${GO_VERSION}.linux-amd64.tar.gz \
    && rm go${GO_VERSION}.linux-amd64.tar.gz

# Install Foundry
SHELL ["/bin/bash", "-c"]

RUN curl -L https://foundry.paradigm.xyz | bash
RUN source ~/.bashrc
ENV PATH="/root/.foundry/bin:${PATH}"
RUN foundryup

RUN mkdir /opstack-temp

RUN cd /opstack-temp/ \
    && git clone https://github.com/ethereum-optimism/op-geth.git \
    && cd op-geth \
    && make geth

# OPStack: 
RUN cd /opstack-temp/ \
    && foundryup \
    && git clone https://github.com/ethereum-optimism/optimism.git \
    && cd optimism \
    && git checkout 8cf093440295ad2c4059f6ada508705013f98eab \
    && pnpm install \
    && make op-node op-batcher op-proposer

RUN cd /opstack-temp/ \
    && cd optimism \
    && pnpm build 

# CLONE ENV INJECTOR & BUILD
RUN cd /opstack-temp/ \
    && git clone https://github.com/shards-dev/tools.git \
    && cd tools \
    && npm install
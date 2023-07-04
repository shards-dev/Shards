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

# Install npm
#RUN apt-get install -y npm

# Install Yarn
RUN npm install --global yarn


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

RUN mkdir /opstack-temp \
    && cd /opstack-temp/ \
    && foundryup \
    && git clone https://github.com/ethereum-optimism/optimism.git \
    && cd optimism \
    && git checkout 6e714be7cce331aff7df7316e674e5c0fca7978c \
    && yarn install \
    && make op-node op-batcher op-proposer \
    && yarn build 

RUN cd /opstack-temp/ \
    && git clone https://github.com/ethereum-optimism/op-geth.git \
    && cd op-geth \
    && make geth

# CLONE ENV INJECTOR & BUILD
RUN cd /opstack-temp/ \
    && git clone https://github.com/shards-dev/tools.git \
    && cd tools \
    && npm install
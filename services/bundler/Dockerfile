FROM --platform=linux/amd64 shardsdotdev/rollup:dev

RUN git clone https://github.com/stackup-wallet/stackup-bundler.git \
    && cd stackup-bundler \
    && make install-dev \
    && make generate-environment \
    && make fetch-wallet


CMD cd stackup-bundler && make dev-private-mode
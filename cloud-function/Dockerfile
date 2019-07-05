ARG PROJECT_ID
ARG CLOUD_SDK_VERSION

FROM alpine as build-base
RUN apk update
RUN apk add curl

FROM build-base as build-go111
ARG GOLANG_VERSION
RUN curl -L -o go.tgz https://golang.org/dl/go${GOLANG_VERSION}.linux-amd64.tar.gz
RUN tar -C /usr/local -xzf go.tgz

FROM build-base as build-python37
ARG PYTHON_VERSION
RUN apk update
RUN apk add gcc libc-dev make
RUN curl -L -o python.tar.xz https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tar.xz
RUN mkdir -p /usr/src/python
RUN tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz
WORKDIR /usr/src/python
RUN ./configure --prefix="/python" --without-ensurepip
RUN make -j "$(nproc)" EXTRA_CFLAGS="-DTHREAD_STACK_SIZE=0x100000"
RUN make install
RUN find /python -type f -a \( -name '*.pyc' -o -name '*.pyo' \) -exec rm -rf '{}' +
RUN find /python -type d -a \( -name test -o -name tests \) -exec rm -rf '{}' +

FROM build-base as build-yarn
ARG YARN_VERSION
RUN curl -L -o yarn.tar.gz https://yarnpkg.com/downloads/${YARN_VERSION}/yarn-v${YARN_VERSION}.tar.gz
RUN mkdir -p /usr/local/yarn
RUN tar -C /usr/local/yarn --strip-components=1 -xzf yarn.tar.gz

FROM build-base as build-nodejs-base
RUN apk update
RUN apk add libstdc++ python g++ gcc make linux-headers

FROM build-nodejs-base as build-nodejs6
ARG NODEJS_6_VERSION
RUN curl -L -o node.tar.xz https://nodejs.org/dist/v${NODEJS_6_VERSION}/node-v${NODEJS_6_VERSION}.tar.xz
RUN mkdir -p /usr/src/node
RUN tar -C /usr/src/node --strip-components=1 -xf node.tar.xz
WORKDIR /usr/src/node
RUN ./configure --prefix="/node"
RUN make -j "$(nproc)"
RUN make install

FROM build-nodejs-base as build-nodejs8
ARG NODEJS_8_VERSION
RUN curl -L -o node.tar.xz https://nodejs.org/dist/v${NODEJS_8_VERSION}/node-v${NODEJS_8_VERSION}.tar.xz
RUN mkdir -p /usr/src/node
RUN tar -C /usr/src/node --strip-components=1 -xf node.tar.xz
WORKDIR /usr/src/node
RUN ./configure --prefix="/node"
RUN make -j "$(nproc)"
RUN make install

FROM build-nodejs-base as build-nodejs10
ARG NODEJS_10_VERSION
RUN curl -L -o node.tar.xz https://nodejs.org/dist/v${NODEJS_10_VERSION}/node-v${NODEJS_10_VERSION}.tar.xz
RUN mkdir -p /usr/src/node
RUN tar -C /usr/src/node --strip-components=1 -xf node.tar.xz
WORKDIR /usr/src/node
RUN ./configure --prefix="/node"
RUN make -j "$(nproc)"
RUN make install

FROM gcr.io/${PROJECT_ID}/cloud-function-builder-cli as cli

FROM google/cloud-sdk:${CLOUD_SDK_VERSION}-alpine as runtime-base
COPY --from=cli /usr/local/bin/cloud-function-builder /usr/local/bin

FROM runtime-base as go111
COPY --from=build-go111 /usr/local/go /usr/local/go
ENV PATH $PATH:/usr/local/go/bin
ENTRYPOINT ["go", "version"]

FROM runtime-base as python37
COPY --from=build-python37 /python /usr/local
ENTRYPOINT ["python3", "--version"]

FROM runtime-base as nodejs-base
RUN apk update
RUN apk add --no-cache libstdc++
COPY --from=build-yarn /usr/local/yarn /usr/local/yarn
ENV PATH $PATH:/usr/local/yarn/bin:/usr/local/node/bin

FROM nodejs-base as nodejs6
COPY --from=build-nodejs6 /node /usr/local/node
ENTRYPOINT ["node", "--version"]

FROM nodejs-base as nodejs8
COPY --from=build-nodejs8 /node /usr/local/node
ENTRYPOINT ["node", "--version"]

FROM nodejs-base as nodejs10
COPY --from=build-nodejs10 /node /usr/local/node
ENTRYPOINT ["node", "--version"]
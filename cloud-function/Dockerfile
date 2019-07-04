ARG CLOUD_SDK_VERSION
FROM google/cloud-sdk:${CLOUD_SDK_VERSION}-slim

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        wget \
		g++ \
		gcc \
		libc6-dev \
		make \
		pkg-config \
        build-essential \
        checkinstall \
        libreadline-gplv2-dev \
        libncursesw5-dev \
        libssl-dev \
        libsqlite3-dev \
        tk-dev \
        libgdbm-dev \
        libc6-dev \
        libbz2-dev \
        libffi-dev \
        zlib1g-dev && \
    rm -rf /var/lib/apt/lists/*

ARG GOLANG_VERSION
ENV GOLANG_VERSION $GOLANG_VERSION

RUN set -eux && \
	url="https://golang.org/dl/go${GOLANG_VERSION}.linux-amd64.tar.gz" && \
	wget -O go.tgz "$url" && \
	tar -C /usr/local -xzf go.tgz && \
	rm go.tgz

ENV PATH $PATH:/usr/local/go/bin
RUN go version

ARG NVM_VERSION
ENV NVM_VERSION $NVM_VERSION

ARG NODEJS_6_VERSION
ENV NODEJS_6_VERSION $NODEJS_6_VERSION

ARG NODEJS_8_VERSION
ENV NODEJS_8_VERSION $NODEJS_8_VERSION

ARG NODEJS_10_VERSION
ENV NODEJS_10_VERSION $NODEJS_10_VERSION

RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v${NVM_VERSION}/install.sh | bash && \
    export NVM_DIR="/root/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" && \
    nvm install $NODEJS_6_VERSION && \
    nvm install $NODEJS_8_VERSION && \
    nvm install $NODEJS_10_VERSION && \
    nvm alias nodejs6 $NODEJS_6_VERSION && \
    nvm alias nodejs8 $NODEJS_8_VERSION && \
    nvm alias nodejs10 $NODEJS_10_VERSION

ENV NVM_DIR /root/.nvm
RUN [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" && \
    nvm --version && \
    nvm use nodejs6 && node --version && \
    nvm use nodejs8 && node --version && \
    nvm use nodejs10 && node --version

ARG YARN_VERSION
ENV YARN_VERSION $YARN_VERSION

RUN [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && \
    apt-get install -y --no-install-recommends yarn && \
    yarn policies set-version $YARN_VERSION

RUN [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" && \
    yarn --version

ARG PYTHON_3_7_VERSION
ENV PYTHON_3_7_VERSION $PYTHON_3_7_VERSION

RUN set -eux && \
	url="https://www.python.org/ftp/python/$PYTHON_3_7_VERSION/Python-$PYTHON_3_7_VERSION.tgz" && \
	wget -O python.tgz "$url" && \
	tar -C /usr/local -xzf python.tgz && \
	rm python.tgz && \
    /usr/local/Python-$PYTHON_3_7_VERSION/configure --enable-optimizations && \
    make altinstall

RUN python3.7 --version

# TODO: Install CLI.

COPY init.sh /bin
ENTRYPOINT /bin/init.sh
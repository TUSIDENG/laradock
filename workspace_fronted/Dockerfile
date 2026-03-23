#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#
# Frontend workspace container for Laradock
#

FROM node:20-alpine

LABEL maintainer="Frontend Workspace"

# Set Environment Variables
ENV DEBIAN_FRONTEND noninteractive
ENV NVM_DIR /home/laradock/.nvm

# Start as root
USER root

# Create laradock user
ARG PUID=1000
ARG PGID=1000

RUN addgroup -g ${PGID} laradock && \
    adduser -u ${PUID} -G laradock -h /home/laradock -s /bin/bash -D laradock

# Install dependencies
RUN apk add --no-cache \
    git \
    curl \
    wget \
    zip \
    unzip \
    bash \
    make \
    g++ \
    python3 \
    py3-pip

# Switch to laradock user
USER laradock
WORKDIR /home/laradock

# Install NVM
ARG NVM_NODEJS_ORG_MIRROR=https://npmmirror.com/mirrors/node
ARG NODE_VERSION=node
ARG INSTALL_NODE=true
ARG NPM_REGISTRY=https://registry.npmmirror.com
ARG NPM_FETCH_RETRIES=2
ARG NPM_FETCH_RETRY_FACTOR=10
ARG NPM_FETCH_RETRY_MINTIMEOUT=10000
ARG NPM_FETCH_RETRY_MAXTIMEOUT=60000

RUN if [ "${INSTALL_NODE}" = true ]; then \
    mkdir -p ${NVM_DIR} && \
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash && \
    . ${NVM_DIR}/nvm.sh && \
    nvm install ${NODE_VERSION} && \
    nvm use ${NODE_VERSION} && \
    nvm alias default ${NODE_VERSION} && \
    npm cache clean --force && \
    npm config set fetch-retries ${NPM_FETCH_RETRIES} && \
    npm config set fetch-retry-factor ${NPM_FETCH_RETRY_FACTOR} && \
    npm config set fetch-retry-mintimeout ${NPM_FETCH_RETRY_MINTIMEOUT} && \
    npm config set fetch-retry-maxtimeout ${NPM_FETCH_RETRY_MAXTIMEOUT} && \
    if [ "${NPM_REGISTRY}" ]; then \
        npm config set registry ${NPM_REGISTRY}; \
    fi; \
fi

# Install Yarn
ARG INSTALL_YARN=true
ARG YARN_VERSION=latest

RUN if [ "${INSTALL_YARN}" = true ]; then \
    . ${NVM_DIR}/nvm.sh && \
    corepack enable && \
    if [ "${YARN_VERSION}" = "latest" ]; then \
        corepack prepare yarn@stable --activate; \
    else \
        corepack prepare yarn@${YARN_VERSION} --activate; \
    fi; \
fi

# Install npm global packages
ARG INSTALL_NPM_GULP=false
ARG INSTALL_NPM_BOWER=false
ARG INSTALL_NPM_VUE_CLI=false
ARG INSTALL_NPM_ANGULAR_CLI=false
ARG INSTALL_NPM_CHECK_UPDATES_CLI=false
ARG INSTALL_PNPM=false

RUN if [ "${INSTALL_NODE}" = true ]; then \
    . ${NVM_DIR}/nvm.sh && \
    if [ "${INSTALL_NPM_GULP}" = true ]; then \
        npm install -g gulp; \
    fi && \
    if [ "${INSTALL_NPM_BOWER}" = true ]; then \
        npm install -g bower; \
    fi && \
    if [ "${INSTALL_NPM_VUE_CLI}" = true ]; then \
        npm install -g @vue/cli; \
    fi && \
    if [ "${INSTALL_NPM_ANGULAR_CLI}" = true ]; then \
        npm install -g @angular/cli; \
    fi && \
    if [ "${INSTALL_NPM_CHECK_UPDATES_CLI}" = true ]; then \
        npm install -g npm-check-updates; \
    fi && \
    if [ "${INSTALL_PNPM}" = true ]; then \
        npm install -g pnpm; \
    fi; \
fi

# Add NVM to bashrc
RUN echo "" >> ~/.bashrc && \
    echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/bash_completion" ] && . "$NVM_DIR/bash_completion"' >> ~/.bashrc

# Add YARN to path
RUN echo "" >> ~/.bashrc && \
    echo 'export YARN_DIR="$HOME/.yarn"' >> ~/.bashrc && \
    echo 'export PATH="$YARN_DIR/bin:$PATH"' >> ~/.bashrc

# Set PATH
ENV PATH ${PATH}:/home/laradock/.nvm/versions/node/$(node -v)/bin:/home/laradock/.yarn/bin

# Set working directory
WORKDIR /var/www

# Expose ports
EXPOSE 3000 4000 5173 8080 8081

# Start bash
CMD ["/bin/bash"]

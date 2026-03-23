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

# Start as root
USER root

###########################################################################
# If you're in China, or you need to change sources, will be set CHANGE_SOURCE to true in .env.
###########################################################################

ARG CHANGE_SOURCE=false
RUN if [ ${CHANGE_SOURCE} = true ]; then \
    # Change Alpine Linux repository to China source
    sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories; \
  fi;

# Create laradock user
ARG PUID=1001
ARG PGID=1001

RUN if getent group laradock > /dev/null 2>&1; then \
    delgroup laradock; \
fi && \
if getent passwd laradock > /dev/null 2>&1; then \
    deluser laradock; \
fi && \
addgroup -g ${PGID} laradock && \
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

# Enable corepack (requires root)
ARG INSTALL_YARN=true
ARG YARN_VERSION=latest

RUN if [ "${INSTALL_YARN}" = true ]; then \
    corepack enable && \
    if [ "${YARN_VERSION}" = "latest" ]; then \
        corepack prepare yarn@stable --activate; \
    else \
        corepack prepare yarn@${YARN_VERSION} --activate; \
    fi; \
fi

# Install npm global packages (requires root)
ARG INSTALL_NPM_GULP=false
ARG INSTALL_NPM_BOWER=false
ARG INSTALL_NPM_VUE_CLI=false
ARG INSTALL_NPM_ANGULAR_CLI=false
ARG INSTALL_NPM_CHECK_UPDATES_CLI=false
ARG INSTALL_PNPM=false

RUN if [ "${INSTALL_NPM_GULP}" = true ]; then \
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
    # Remove existing pnpm and pnpx symlinks created by corepack
    rm -f /usr/local/bin/pnpm /usr/local/bin/pnpx && \
    npm install -g pnpm; \
fi

# Switch to laradock user
USER laradock
WORKDIR /home/laradock

# Configure npm
ARG NPM_REGISTRY=https://registry.npmmirror.com
ARG NPM_FETCH_RETRIES=2
ARG NPM_FETCH_RETRY_FACTOR=10
ARG NPM_FETCH_RETRY_MINTIMEOUT=10000
ARG NPM_FETCH_RETRY_MAXTIMEOUT=60000

RUN npm config set fetch-retries ${NPM_FETCH_RETRIES} && \
    npm config set fetch-retry-factor ${NPM_FETCH_RETRY_FACTOR} && \
    npm config set fetch-retry-mintimeout ${NPM_FETCH_RETRY_MINTIMEOUT} && \
    npm config set fetch-retry-maxtimeout ${NPM_FETCH_RETRY_MAXTIMEOUT} && \
    if [ "${NPM_REGISTRY}" ]; then \
        npm config set registry ${NPM_REGISTRY}; \
    fi

# Add YARN to path
RUN echo "" >> ~/.bashrc && \
    echo 'export YARN_DIR="$HOME/.yarn"' >> ~/.bashrc && \
    echo 'export PATH="$YARN_DIR/bin:$PATH"' >> ~/.bashrc

# Set working directory
WORKDIR /var/www

# Expose ports
EXPOSE 3000 4000 5173 8080 8081

# Start bash
CMD ["/bin/bash"]

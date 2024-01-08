FROM ubuntu:latest
LABEL maintainer="xin shan <leggod.com@gmail.com>"
RUN sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list

USER root

ARG LARADOCK_PHP_VERSION
ENV LARADOCK_PHP_VERSION ${PHP_VERSION}
# always run apt update when start and after add new source list, then clean up at end.
# 安装软件包以添加外部仓库
RUN apt-get update && apt-get install -yqq software-properties-common

# 添加 ppa:ondrej/php 仓库
RUN add-apt-repository ppa:ondrej/php

# 安装 PHP 8.2 和必要的扩展
RUN apt-get update && apt-get install -yqq php${LARADOCK_PHP_VERSION} php${LARADOCK_PHP_VERSION}-cli php${LARADOCK_PHP_VERSION}-common php${LARADOCK_PHP_VERSION}-json php${LARADOCK_PHP_VERSION}-phar


###########################################################################
# Set Timezone
###########################################################################

ARG TZ=UTC
ENV TZ ${TZ}

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

###########################################################################
# Node / NVM:
###########################################################################

ARG INSTALL_NODE=false
ARG NODE_VERSION
ENV NODE_VERSION=${NODE_VERSION}
ENV NVM_DIR=/root/.nvm
ARG NPM_REGISTRY
ENV NPM_REGISTRY=${NPM_REGISTRY}
ARG NVM_NODEJS_ORG_MIRROR
ENV NVM_NODEJS_ORG_MIRROR ${NVM_NODEJS_ORG_MIRROR}

RUN if [ ${INSTALL_NODE} = true ]; then \
    # Install nvm 
    mkdir -p $NVM_DIR && \
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash \
        && . $NVM_DIR/nvm.sh \
        && nvm install ${NODE_VERSION} --lts \ 
        && nvm use ${NODE_VERSION} \
        && if [ ${NPM_REGISTRY} ]; then \
            npm config set registry ${NPM_REGISTRY} \
        ;fi \
        && if [ ${INSTALL_NPM_VUE_CLI} = true ]; then \
            npm install -g @vue/cli \
        ;fi \
        && if [ ${INSTALL_NPM_HEXO} = true ]; then \
            npm install -g hexo \
        ;fi \
;fi

RUN if [ ${INSTALL_NODE} = true ]; then \
    echo "" >> ~/.bashrc && \
    echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm' >> ~/.bashrc \
;fi


# Add PATH for node
ENV PATH $PATH:/root/.node-bin

RUN if [ ${INSTALL_NODE} = true ]; then \
    find $NVM_DIR -type f -name node -exec ln -s {} /usr/local/bin/node \; && \
    NODE_MODS_DIR="$NVM_DIR/versions/node/$(node -v)/lib/node_modules" && \
    ln -s $NODE_MODS_DIR/npm/bin/npm-cli.js /usr/local/bin/npm && \
    ln -s $NODE_MODS_DIR/npm/bin/npx-cli.js /usr/local/bin/npx && \
    ln -s $NODE_MODS_DIR/vue-cli/bin/vue /usr/local/bin/vue \
;fi

###########################################################################
# PNPM:
###########################################################################


ARG INSTALL_PNPM=false

RUN if [ ${INSTALL_PNPM} = true ]; then \
    npm install -g pnpm \
;fi

###########################################################################
# PYTHON3:
###########################################################################

ARG INSTALL_PYTHON3=false

RUN if [ ${INSTALL_PYTHON3} = true ]; then \
  apt-get -y install python3 python3-dev build-essential  \
  && curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py  \
  && python3 get-pip.py  \
  && rm get-pip.py  \
  && python3 -m pip install --upgrade --force-reinstall pip  \
  && python3 -m pip install --upgrade virtualenv \
;fi

###########################################################################
# Composer:
###########################################################################


# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Add the composer.json
COPY ./composer.json /root/.composer/composer.json

# Add the auth.json for Magento 2 credentials
COPY ./auth.json /root/.composer/auth.json

# Make sure that ~/.composer belongs to root
RUN chown -R root:root /root/.composer

# Export composer vendor path
RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="$HOME/.composer/vendor/bin:$PATH"' >> ~/.bashrc

# Update composer
ARG COMPOSER_VERSION=2
ENV COMPOSER_VERSION ${COMPOSER_VERSION}
RUN set -eux; \
    if [ "$COMPOSER_VERSION" = "1" ] || [ "$COMPOSER_VERSION" = "2" ] || [ "$COMPOSER_VERSION" = "2.2" ]; then \
        composer self-update --${COMPOSER_VERSION}; \
    else \
        composer self-update ${COMPOSER_VERSION}; \
    fi



# Check if global install need to be ran
ARG COMPOSER_GLOBAL_INSTALL=false
ENV COMPOSER_GLOBAL_INSTALL ${COMPOSER_GLOBAL_INSTALL}

RUN if [ ${COMPOSER_GLOBAL_INSTALL} = true ]; then \
    # run the install
    composer global install \
;fi

# Check if auth file is disabled
ARG COMPOSER_AUTH_JSON=false
ENV COMPOSER_AUTH_JSON ${COMPOSER_AUTH_JSON}

RUN if [ ${COMPOSER_AUTH_JSON} = false ]; then \
    # remove the file
    rm /home/laradock/.composer/auth.json \
;fi

ARG COMPOSER_REPO_PACKAGIST
ENV COMPOSER_REPO_PACKAGIST ${COMPOSER_REPO_PACKAGIST}

RUN if [ ${COMPOSER_REPO_PACKAGIST} ]; then \
    composer config -g repo.packagist composer ${COMPOSER_REPO_PACKAGIST} \
;fi

# Export composer vendor path
RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="~/.composer/vendor/bin:$PATH"' >> ~/.bashrc


#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

# Set default work directory
WORKDIR /var/www

ENTRYPOINT ["/bin/bash"]

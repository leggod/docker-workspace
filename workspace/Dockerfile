FROM ubuntu:rolling
LABEL maintainer="xin shan <leggod.com@gmail.com>"

RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    sed -i s/security.ubuntu.com/mirrors.aliyun.com/g /etc/apt/sources.list

# Install apt packages
RUN apt update         && \
    apt-get install -y curl \
        git && \
    apt-get clean
    
ENV DEBIAN_FRONTEND=noninteractive

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
    curl -o- https://ghproxy.com/https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash \
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

# # Add the composer.json
# COPY ./composer.json /root/.composer/composer.json

# # Add the auth.json for magento 2 credentials
# COPY ./auth.json /root/.composer/auth.json

# # Make sure that ~/.composer belongs to laradock
# RUN chown -R root:root /root/.composer

# # Export composer vendor path
# RUN echo "" >> ~/.bashrc && \
#     echo 'export PATH="$HOME/.composer/vendor/bin:$PATH"' >> ~/.bashrc

# # Update composer
# ARG COMPOSER_VERSION=2
# ENV COMPOSER_VERSION ${COMPOSER_VERSION}
# RUN set -eux; \
#       if [ "$COMPOSER_VERSION" = "1" ] || [ "$COMPOSER_VERSION" = "2" ]; then \
#           composer self-update --${COMPOSER_VERSION}; \
#       else \
#           composer self-update ${COMPOSER_VERSION}; \
#       fi

# # Check if auth file is disabled
# ARG COMPOSER_AUTH=false
# ENV COMPOSER_AUTH ${COMPOSER_AUTH}

# RUN if [ ${COMPOSER_AUTH} = false ]; then \
#     # remove the file
#     rm /home/laradock/.composer/auth.json \
# ;fi

# ARG COMPOSER_REPO_PACKAGIST
# ENV COMPOSER_REPO_PACKAGIST ${COMPOSER_REPO_PACKAGIST}

# RUN if [ ${COMPOSER_REPO_PACKAGIST} ]; then \
#     composer config -g repo.packagist composer ${COMPOSER_REPO_PACKAGIST} \
# ;fi

# # Export composer vendor path
# RUN echo "" >> ~/.bashrc && \
#     echo 'export PATH="~/.composer/vendor/bin:$PATH"' >> ~/.bashrc


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

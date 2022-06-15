FROM ubuntu:rolling
LABEL maintainer="xin shan <leggod.com@gmail.com>"

RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    sed -i s/security.ubuntu.com/mirrors.aliyun.com/g /etc/apt/sources.list

# Install apt packages
RUN apt update         && \
    apt-get install -y curl && \
    apt-get clean

ARG INSTALL_NODE=false
ARG NODE_VERSION
ENV NODE_VERSION=${NODE_VERSION}
ENV NVM_DIR=/root/.nvm
ARG NPM_REGISTRY
ENV NPM_REGISTRY=${NPM_REGISTRY}


RUN if [ ${INSTALL_NODE} = true ]; then \
    # Install nvm 
    mkdir -p $NVM_DIR && \
    curl -o- https://ghproxy.com/https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash \
        && . $NVM_DIR/nvm.sh \
        && nvm install ${NODE_VERSION} --lts \ 
        # && nvm use ${NODE_VERSION} \
        # && if [ ${NPM_REGISTRY} ]; then \
        # npm config set registry ${NPM_REGISTRY} \
        # ;fi \
        # && ln -s `npm bin --global` ~/.node-bin \
;fi

RUN if [ ${INSTALL_NODE} = true ]; then \
    # nvm
    echo 'export NVM_DIR="$HOME/.nvm"' >> "$HOME/.bashrc" && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm' >> "$HOME/.bashrc" && \
    echo '[ -s "$NVM_DIR/bash_completion" ] && . "$NVM_DIR/bash_completion" # This loads nvm bash_completion' >> "$HOME/.bashrc" \
;fi

# nodejs

# RUN if [ ${INSTALL_NODE} = true ]; then \
#     bash -c 'source ~/.profile' && \
    
# ;fi

#
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

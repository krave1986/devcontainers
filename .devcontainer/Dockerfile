FROM ubuntu:latest
RUN apt update && \
    apt upgrade --assume-yes && \
    apt install --assume-yes locales man-db wget git && \
    locale-gen "en_US.UTF-8" && \
    dpkg-reconfigure -f noninteractive locales && \
    yes | unminimize && \
    wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash && \
    export NVM_DIR="$HOME/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \
    \. "$NVM_DIR/nvm.sh" && \
    [ -s "$NVM_DIR/bash_completion" ] && \
    \. "$NVM_DIR/bash_completion" && \
    nvm install --lts
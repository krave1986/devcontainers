FROM ubuntu:latest
RUN apt update && \
    apt upgrade && \
    apt install --assume-yes wget git && \
    wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash && \
    export NVM_DIR="$HOME/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \
    \. "$NVM_DIR/nvm.sh" && \
    [ -s "$NVM_DIR/bash_completion" ] && \
    \. "$NVM_DIR/bash_completion" && \
    nvm install 12\
FROM ubuntu:latest

ARG USERNAME=nodejs
ARG USER_UID=1000
ARG USER_GID=$USER_GID

RUN groupadd --gid $USER_GID $USERNAME && \
    useradd --uid $USER_UID --gid $USER_GID -m $USERNAME && \
    apt update && \
    apt upgrade --assume-yes && \
    apt install --assume-yes sudo && \
    echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME && \
    chmod 0440 /etc/sudoers.d/$USERNAME
    sudo -u $USERNAME apt install --assume-yes locales man-db wget git && \
    sudo -u $USERNAME locale-gen "en_US.UTF-8" && \
    sudo -u $USERNAME dpkg-reconfigure -f noninteractive locales && \
    sudo -u $USERNAME yes | unminimize && \
    sudo -u $USERNAME wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash && \
    sudo -u $USERNAME export NVM_DIR="$HOME/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \
    \. "$NVM_DIR/nvm.sh" && \
    [ -s "$NVM_DIR/bash_completion" ] && \
    \. "$NVM_DIR/bash_completion" && \
    sudo -u $USERNAME nvm install --lts
FROM ubuntu:latest

ENV PATH="/root/.local/share/fnm:$PATH"

RUN apt-get update && apt-get install -y curl unzip libatomic1 wget \
    && curl -fsSL https://fnm.vercel.app/install | bash \
    && fnm install --latest && fnm default latest \
    && wget -qO- https://get.pnpm.io/install.sh | ENV="$HOME/.bashrc" SHELL="$(which bash)" bash - \
    && echo 'eval "$(fnm env)"' >> /root/.bashrc \
    && rm -rf /var/lib/apt/lists/*
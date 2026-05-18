FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    zsh \
    curl \
    git \
    npm \
    sudo \
    fzf \
    tmux \
    vim \
    unzip \
    wget \
    && rm -rf /var/lib/apt/lists/*

RUN useradd -ms /bin/zsh user \
    && echo "user ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER user
WORKDIR /home/user

SHELL ["/usr/bin/zsh", "-c"]

COPY --from=jdxcode/mise /usr/local/bin/mise /usr/local/bin/

RUN echo 'eval "$(mise activate zsh)"' >> /home/user/.zshrc

ENV PATH="/home/user/.local/share/mise/shims:/home/user/.local/bin:$PATH"

RUN mkdir -p /home/user/.config/mise

COPY --chown=user:user mise.toml /home/user/.config/mise/config.toml

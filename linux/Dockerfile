FROM archlinux:latest

ENV http_proxy="" \
    https_proxy="" \
    ftp_proxy="" \
    all_proxy="" \
    HTTP_PROXY="" \
    HTTPS_PROXY="" \
    FTP_PROXY="" \
    ALL_PROXY="" \
    NVM_DIR="/root/.nvm" \
    CARGO_HOME="/root/.cargo" \
    RUSTUP_HOME="/root/.rustup" \
    PATH="/root/.cargo/bin:/root/.nvm/versions/node/v24.14.1/bin:${PATH}"

# 更新系统并安装基础工具 + SSH 服务
RUN if grep -q '^#\?DisableSandbox' /etc/pacman.conf; then \
        sed -i 's/^#\?DisableSandbox.*/DisableSandbox/' /etc/pacman.conf; \
    else \
        printf '\nDisableSandbox\n' >> /etc/pacman.conf; \
    fi \
    && pacman -Syu --noconfirm \
    && pacman -S --noconfirm --needed \
    git \
    curl \
    vim \
    base-devel \
    python \
    openssh \
    ca-certificates \
    bash \
    sed \
    shadow \
    && pacman -Scc --noconfirm

# 初始化 root 的 bash 配置，默认加载 nvm 和 rust 环境
RUN touch /root/.bashrc \
    && printf '\nexport NVM_DIR="/root/.nvm"\n[ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"\n[ -s "$NVM_DIR/bash_completion" ] && \\. "$NVM_DIR/bash_completion"\n[ -s "/root/.cargo/env" ] && . "/root/.cargo/env"\n' >> /root/.bashrc

# 安装 nvm 和 Node.js v24.14.1
RUN export PROFILE=/root/.bashrc \
    && curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash \
    && . "$NVM_DIR/nvm.sh" \
    && nvm install 24.14.1 \
    && nvm alias default 24.14.1 \
    && nvm use default

# 安装最新版本 Rust
RUN curl https://sh.rustup.rs -sSf | bash -s -- -y

# 创建 sshd 运行所需的目录并生成主机密钥
RUN mkdir -p /var/run/sshd \
    && ssh-keygen -A

# 设置 root 用户的密码 (这里设为 'root'，方便本地开发)
RUN echo 'root:root' | chpasswd

# 修改 SSH 配置文件，允许 root 用户密码登录
RUN sed -i 's/^#\?PermitRootLogin .*/PermitRootLogin yes/' /etc/ssh/sshd_config

# 设置工作目录
WORKDIR /workspace

# 声明暴露的端口 (仅作为文档参考，实际映射在 compose 中完成)
EXPOSE 22

CMD ["/usr/bin/sshd", "-D"]

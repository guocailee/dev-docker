FROM ubuntu:22.04
ENV http_proxy="" \
    https_proxy="" \
    ftp_proxy="" \
    all_proxy="" \
    HTTP_PROXY="" \
    HTTPS_PROXY="" \
    FTP_PROXY="" \
    ALL_PROXY=""
# 1. 更新源并安装基础工具 + SSH 服务
RUN apt-get update && apt-get install -y \
    git \
    curl \
    vim \
    build-essential \
    python3 \
    openssh-server \
    && rm -rf /var/lib/apt/lists/*

# 2. 创建 sshd 运行所需的目录
RUN mkdir /var/run/sshd

# 3. 设置 root 用户的密码 (这里设为 'root'，方便本地开发)
RUN echo 'root:root' | chpasswd

# 4. 修改 SSH 配置文件，允许 root 用户密码登录
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# 设置工作目录
WORKDIR /workspace

# 5. 声明暴露的端口 (仅作为文档参考，实际映射在 compose 中完成)
EXPOSE 22

# 6. 启动 SSH 服务并在前台运行，这会替换掉之前的 sleep infinity
CMD ["/usr/sbin/sshd", "-D"]
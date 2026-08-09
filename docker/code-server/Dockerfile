FROM lscr.io/linuxserver/code-server:latest

ENV DEBIAN_FRONTEND=noninteractive
ENV HOMELAB_DEV_STAGE=stage1

# Base development tools
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    git \
    git-lfs \
    jq \
    unzip \
    zip \
    make \
    build-essential \
    python3 \
    python3-pip \
    python3-venv \
    python3-dev \
    ca-certificates \
    gnupg \
    lsb-release \
    software-properties-common \
    openssh-client \
    sudo \
    nano \
    vim \
    tmux \
    htop \
    tree \
    dnsutils \
    netcat-openbsd \
    && rm -rf /var/lib/apt/lists/*

# GitHub CLI
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
    | tee /usr/share/keyrings/githubcli-archive-keyring.gpg >/dev/null && \
    chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
    | tee /etc/apt/sources.list.d/github-cli.list >/dev/null && \
    apt-get update && \
    apt-get install -y gh && \
    rm -rf /var/lib/apt/lists/*

# Google Cloud CLI

RUN curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg \
    | gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" \
    | tee /etc/apt/sources.list.d/google-cloud-sdk.list && \
    apt-get update && \
    apt-get install -y google-cloud-cli && \
    rm -rf /var/lib/apt/lists/*

# kubectl
RUN KUBECTL_VERSION="$(curl -fsSL https://dl.k8s.io/release/stable.txt)" && \
    curl -fsSL "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl" \
    -o /usr/local/bin/kubectl && \
    chmod +x /usr/local/bin/kubectl

# Helm
RUN curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 \
    | bash

# Terraform
RUN curl -fsSL https://apt.releases.hashicorp.com/gpg \
    | gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(. /etc/os-release && echo "$VERSION_CODENAME") main" \
    > /etc/apt/sources.list.d/hashicorp.list && \
    apt-get update && \
    apt-get install -y terraform && \
    rm -rf /var/lib/apt/lists/*

# OpenTofu
RUN curl --proto '=https' --tlsv1.2 -fsSL \
    https://get.opentofu.org/install-opentofu.sh \
    -o /tmp/install-opentofu.sh && \
    chmod +x /tmp/install-opentofu.sh && \
    /tmp/install-opentofu.sh --install-method standalone && \
    rm -f /tmp/install-opentofu.sh

# k9s
RUN K9S_VERSION="$(curl -fsSL https://api.github.com/repos/derailed/k9s/releases/latest |     python3 -c 'import json,sys; print(json.load(sys.stdin)["tag_name"])')" &&     curl -fsSL "https://github.com/derailed/k9s/releases/download/${K9S_VERSION}/k9s_Linux_amd64.tar.gz"     -o /tmp/k9s.tar.gz &&     tar -xzf /tmp/k9s.tar.gz -C /tmp k9s &&     install -m 0755 /tmp/k9s /usr/local/bin/k9s &&     rm -f /tmp/k9s.tar.gz /tmp/k9s

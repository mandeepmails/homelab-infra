FROM lscr.io/linuxserver/code-server:latest

ARG TERRAFORM_VERSION=1.15.8
ARG TERRAGRUNT_VERSION=1.1.1
ARG K9S_VERSION=0.51.0
ARG KUBECTL_VERSION=v1.35.6

ENV DEBIAN_FRONTEND=noninteractive

# ------------------------------------------------------------
# Base developer packages
# ------------------------------------------------------------

RUN apt-get update && apt-get install -y \
    ca-certificates \
    curl \
    wget \
    gnupg \
    git \
    git-lfs \
    jq \
    unzip \
    zip \
    make \
    gcc \
    g++ \
    shellcheck \
    python3 \
    python3-pip \
    python3-venv \
    pipx \
    build-essential \
    openssh-client \
    && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------
# Node.js 22
# ------------------------------------------------------------

RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get update \
    && apt-get install -y nodejs \
    && npm install -g npm@latest \
    && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------
# Terraform
# ------------------------------------------------------------

RUN install -m 0755 -d /etc/apt/keyrings \
    && curl -fsSL https://apt.releases.hashicorp.com/gpg \
       | gpg --dearmor -o /etc/apt/keyrings/hashicorp.gpg \
    && chmod a+r /etc/apt/keyrings/hashicorp.gpg \
    && echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/hashicorp.gpg] https://apt.releases.hashicorp.com noble main" \
       > /etc/apt/sources.list.d/hashicorp.list \
    && apt-get update \
    && apt-get install -y terraform=${TERRAFORM_VERSION}-1 \
    && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------
# OpenTofu
#
# Official OpenTofu Debian repository.
# ------------------------------------------------------------

RUN install -m 0755 -d /etc/apt/keyrings \
    && curl -fsSL https://get.opentofu.org/opentofu.gpg \
       | tee /etc/apt/keyrings/opentofu.gpg >/dev/null \
    && curl -fsSL https://packages.opentofu.org/opentofu/tofu/gpgkey \
       | gpg --no-tty --batch --dearmor \
       -o /etc/apt/keyrings/opentofu-repo.gpg \
    && chmod a+r \
       /etc/apt/keyrings/opentofu.gpg \
       /etc/apt/keyrings/opentofu-repo.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/opentofu.gpg,/etc/apt/keyrings/opentofu-repo.gpg] https://packages.opentofu.org/opentofu/tofu/any/ any main" \
       > /etc/apt/sources.list.d/opentofu.list \
    && apt-get update \
    && apt-get install -y tofu \
    && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------
# kubectl
# ------------------------------------------------------------

RUN curl -fsSL \
    "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl" \
    -o /usr/local/bin/kubectl \
    && chmod 0755 /usr/local/bin/kubectl

# ------------------------------------------------------------
# Helm
# ------------------------------------------------------------

RUN curl -fsSL \
    https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 \
    -o /tmp/get-helm.sh \
    && chmod 0700 /tmp/get-helm.sh \
    && /tmp/get-helm.sh \
    && rm -f /tmp/get-helm.sh

# ------------------------------------------------------------
# k9s
# ------------------------------------------------------------

RUN curl -fsSL \
    "https://github.com/derailed/k9s/releases/download/v${K9S_VERSION}/k9s_Linux_amd64.tar.gz" \
    -o /tmp/k9s.tar.gz \
    && tar -xzf /tmp/k9s.tar.gz -C /tmp k9s \
    && install -m 0755 /tmp/k9s /usr/local/bin/k9s \
    && rm -f /tmp/k9s.tar.gz /tmp/k9s

# ------------------------------------------------------------
# Terragrunt
# ------------------------------------------------------------

RUN curl -fsSL \
    "https://github.com/gruntwork-io/terragrunt/releases/download/v${TERRAGRUNT_VERSION}/terragrunt_linux_amd64" \
    -o /usr/local/bin/terragrunt \
    && chmod 0755 /usr/local/bin/terragrunt

# ------------------------------------------------------------
# Google Cloud CLI
# ------------------------------------------------------------

RUN install -m 0755 -d /etc/apt/keyrings \
    && curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg \
       | gpg --dearmor -o /etc/apt/keyrings/cloud.google.gpg \
    && chmod a+r /etc/apt/keyrings/cloud.google.gpg \
    && echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" \
       > /etc/apt/sources.list.d/google-cloud-sdk.list \
    && apt-get update \
    && apt-get install -y google-cloud-cli \
    && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------
# GitHub CLI
# ------------------------------------------------------------

RUN curl -fsSL \
    https://cli.github.com/packages/githubcli-archive-keyring.gpg \
    -o /usr/share/keyrings/githubcli-archive-keyring.gpg \
    && chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
    && echo "deb [arch=amd64 signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
       > /etc/apt/sources.list.d/github-cli.list \
    && apt-get update \
    && apt-get install -y gh \
    && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------
# yq
# ------------------------------------------------------------

RUN YQ_VERSION="$(curl -fsSL https://api.github.com/repos/mikefarah/yq/releases/latest | jq -r .tag_name)" \
    && curl -fsSL \
       "https://github.com/mikefarah/yq/releases/download/${YQ_VERSION}/yq_linux_amd64" \
       -o /usr/local/bin/yq \
    && chmod 0755 /usr/local/bin/yq

# ------------------------------------------------------------
# GitLab CLI
# ------------------------------------------------------------

RUN GLAB_VERSION="$(curl -fsSL https://api.github.com/repos/gitlab-org/cli/releases/latest | jq -r .tag_name | sed 's/^v//')" \
    && curl -fsSL \
       "https://gitlab.com/gitlab-org/cli/-/releases/v${GLAB_VERSION}/downloads/glab_${GLAB_VERSION}_Linux_x86_64.tar.gz" \
       -o /tmp/glab.tar.gz \
    && tar -xzf /tmp/glab.tar.gz -C /tmp \
    && install -m 0755 /tmp/bin/glab /usr/local/bin/glab \
    && rm -rf /tmp/glab.tar.gz /tmp/bin

# ------------------------------------------------------------
# Environment
# ------------------------------------------------------------

ENV PATH="/usr/local/bin:${PATH}"

WORKDIR /workspace

RUN git lfs install --system || true

# ------------------------------------------------------------
# Build-time verification
# ------------------------------------------------------------

RUN echo "=== TERRAFORM ===" \
    && terraform version \
    && echo "=== OPENTOFU ===" \
    && tofu version \
    && echo "=== TERRAGRUNT ===" \
    && terragrunt --version \
    && echo "=== KUBECTL ===" \
    && kubectl version --client \
    && echo "=== HELM ===" \
    && helm version --short \
    && echo "=== K9S ===" \
    && k9s version --short \
    && echo "=== GCLOUD ===" \
    && gcloud version \
    && echo "=== GH ===" \
    && gh --version \
    && echo "=== GLAB ===" \
    && glab --version \
    && echo "=== YQ ===" \
    && yq --version \
    && echo "=== NODE ===" \
    && node --version \
    && echo "=== NPM ===" \
    && npm --version \
    && echo "=== PYTHON ===" \
    && python3 --version

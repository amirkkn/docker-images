FROM jenkins/inbound-agent:4.13-1

USER root

ARG VAULT_VERSION=1.5.3
ARG PACKER_VERSION=1.6.2
ARG TERRAFORM_012_VERSION=0.12.29
ARG TERRAFORM_013_VERSION=0.13.5
ARG TERRAFORM_014_VERSION=0.14.5
ARG TERRAFORM_015_VERSION=0.15.4
ARG TERRAFORM_1_VERSION=1.0.5
ARG KUBECTL_VERSION=1.21.9
ARG HELM_VERSION=3.5.1
ARG ANSIBLE_VERSION=2.10.3
ARG TERRAFORM_DOCS_VERSION=0.10.1

RUN apt-get update && apt-get dist-upgrade -y \
      && apt-get install -y \
        git \
        apt-transport-https \
        curl \
        init \
        openssh-server openssh-client \
        software-properties-common \
        unzip \
        jq \
        python3-pip \
      && rm -rf /var/lib/apt/lists/* \

    #### install aws cli
    && curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
      && unzip awscliv2.zip && ./aws/install && rm awscliv2.zip && rm -rf aws \

    #### install boto3
    && pip3 install --no-cache-dir -U boto3 checkov pre-commit \

    #### install ansible
    && pip3 install --no-cache-dir ansible==${ANSIBLE_VERSION} \

    #### install vault
    && curl "https://releases.hashicorp.com/vault/${VAULT_VERSION}/vault_${VAULT_VERSION}_linux_amd64.zip" -o "vault.zip" \
      && unzip vault.zip && mv vault /usr/bin && rm vault.zip \

    #### install packer
    && curl "https://releases.hashicorp.com/packer/${PACKER_VERSION}/packer_${PACKER_VERSION}_linux_amd64.zip" -o "packer.zip" \
      && unzip packer.zip && mv packer /usr/bin && rm packer.zip \

    #### install kubectl
    && curl "https://storage.googleapis.com/kubernetes-release/release/v${KUBECTL_VERSION}/bin/linux/amd64/kubectl" -o "kubectl" \
      && chmod +x kubectl && mv kubectl /usr/bin \

    #### install helm
    && curl -L "https://get.helm.sh/helm-v${HELM_VERSION}-linux-amd64.tar.gz" -o "helm.tar.gz" \
      && tar -xvzf helm.tar.gz && chmod +x linux-amd64/helm && mv linux-amd64/helm /usr/bin \
      && rm -rf linux-amd64 helm.tar.gz \

    #### install terraform-docs
    && wget https://github.com/terraform-docs/terraform-docs/releases/download/v${TERRAFORM_DOCS_VERSION}/terraform-docs-v${TERRAFORM_DOCS_VERSION}-linux-amd64 \
    && mv terraform-docs-v${TERRAFORM_DOCS_VERSION}-linux-amd64 /usr/local/bin/terraform-docs \
    && chmod a+x /usr/local/bin/terraform-docs

RUN mkdir -p /etc/tfenv \
  && git clone --depth 1 https://github.com/tfutils/tfenv.git /etc/tfenv \
  && chown -R jenkins /etc/tfenv

USER jenkins
    #### install terraform with tfenv
ENV PATH "$PATH:/etc/tfenv/bin"
RUN tfenv install ${TERRAFORM_012_VERSION} \
      && tfenv install ${TERRAFORM_013_VERSION} \
      && tfenv install ${TERRAFORM_014_VERSION} \
      && tfenv install ${TERRAFORM_015_VERSION} \
      && tfenv install ${TERRAFORM_1_VERSION} \
      && tfenv use ${TERRAFORM_1_VERSION}


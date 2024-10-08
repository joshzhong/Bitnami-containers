# Copyright Broadcom, Inc. All Rights Reserved.
# SPDX-License-Identifier: APACHE-2.0

FROM docker.io/bitnami/minideb:bookworm

ARG TARGETARCH

LABEL com.vmware.cp.artifact.flavor="sha256:c50c90cfd9d12b445b011e6ad529f1ad3daea45c26d20b00732fae3cd71f6a83" \
      org.opencontainers.image.base.name="docker.io/bitnami/minideb:bookworm" \
      org.opencontainers.image.created="2024-09-18T06:38:38Z" \
      org.opencontainers.image.description="Application packaged by Broadcom, Inc." \
      org.opencontainers.image.documentation="https://github.com/bitnami/containers/tree/main/bitnami/metallb-speaker/README.md" \
      org.opencontainers.image.licenses="Apache-2.0" \
      org.opencontainers.image.ref.name="0.14.8-debian-12-r8" \
      org.opencontainers.image.source="https://github.com/bitnami/containers/tree/main/bitnami/metallb-speaker" \
      org.opencontainers.image.title="metallb-speaker" \
      org.opencontainers.image.vendor="Broadcom, Inc." \
      org.opencontainers.image.version="0.14.8"

ENV HOME="/" \
    OS_ARCH="${TARGETARCH:-amd64}" \
    OS_FLAVOUR="debian-12" \
    OS_NAME="linux"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
# Install required system packages and dependencies
RUN install_packages ca-certificates curl procps tcpdump
RUN mkdir -p /tmp/bitnami/pkg/cache/ ; cd /tmp/bitnami/pkg/cache/ ; \
    COMPONENTS=( \
      "metallb-speaker-0.14.8-2-linux-${OS_ARCH}-debian-12" \
    ) ; \
    for COMPONENT in "${COMPONENTS[@]}"; do \
      if [ ! -f "${COMPONENT}.tar.gz" ]; then \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz" -O ; \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz.sha256" -O ; \
      fi ; \
      sha256sum -c "${COMPONENT}.tar.gz.sha256" ; \
      tar -zxf "${COMPONENT}.tar.gz" -C /opt/bitnami --strip-components=2 --no-same-owner --wildcards '*/files' ; \
      rm -rf "${COMPONENT}".tar.gz{,.sha256} ; \
    done
RUN apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami
RUN find / -perm /6000 -type f -exec chmod a-s {} \; || true
RUN curl -sLO "https://deb.frrouting.org/frr/pool/frr-9.1/f/frr/frr_9.1.1-0~deb12u1_${OS_ARCH}.deb" && \
    curl -sLO "https://deb.frrouting.org/frr/pool/frr-9.1/f/frr/frr-pythontools_9.1.1-0~deb12u1_all.deb" && \
    curl -sLO "https://deb.frrouting.org/frr/pool/frr-9.1/liby/libyang2/libyang2_2.1.128-2~deb12u1_${OS_ARCH}.deb" && \
    if [ "$OS_ARCH" = "amd64" ]; then \
      echo "c2cd512491ffe6f71dca4e6a790d445faa22578c937636cb462b664f63edc57c  frr_9.1.1-0~deb12u1_${OS_ARCH}.deb" | sha256sum -c - ; \
      echo "62380f148e22c87d91bf24d0ef74484bf592922fbe891a6d62ddcedd19dd9311  libyang2_2.1.128-2~deb12u1_${OS_ARCH}.deb" | sha256sum -c - ; \
    else \
      echo "b214a890382d341d80ff419c8372cbf89fa7ae9ecc61550f0395ffe7325b3e57  frr_9.1.1-0~deb12u1_${OS_ARCH}.deb" | sha256sum -c - ; \
      echo "0e10ece05bab6b777bfac13d62f71b61e504f64d74ce35746dd3abd6c05beb4b  libyang2_2.1.128-2~deb12u1_${OS_ARCH}.deb" | sha256sum -c - ; \
    fi && \
    echo "b520900b1cc08c843e22194781ec21bcca98e46b8bb6a6c9d19a405cb69ac9df  frr-pythontools_9.1.1-0~deb12u1_all.deb" | sha256sum -c - && \
    install_packages "./frr_9.1.1-0~deb12u1_${OS_ARCH}.deb" "./frr-pythontools_9.1.1-0~deb12u1_all.deb" "./libyang2_2.1.128-2~deb12u1_${OS_ARCH}.deb" && \
    rm frr_9.1.1-0~deb12u1_${OS_ARCH}.deb frr-pythontools_9.1.1-0~deb12u1_all.deb libyang2_2.1.128-2~deb12u1_${OS_ARCH}.deb

ENV APP_VERSION="0.14.8" \
    BITNAMI_APP_NAME="metallb-speaker" \
    PATH="/opt/bitnami/metallb-speaker/bin:$PATH"

USER 1001
ENTRYPOINT [ "speaker" ]
CMD [ "--help" ]

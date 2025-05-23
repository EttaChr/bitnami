# Copyright VMware, Inc.
# SPDX-License-Identifier: APACHE-2.0

FROM bitnami/minideb:bullseye as builder

ARG TARGETARCH

ENV HOME="/root" \
    OS_ARCH="${TARGETARCH:-amd64}"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install required system packages and dependencies
RUN install_packages ca-certificates curl
RUN mkdir -p /tmp/bitnami/pkg/cache/ && cd /tmp/bitnami/pkg/cache/ && \
    COMPONENTS=( \
      "kaniko-1.13.0-3-linux-${OS_ARCH}-debian-11" \
    ) && \
    for COMPONENT in "${COMPONENTS[@]}"; do \
      if [ ! -f "${COMPONENT}.tar.gz" ]; then \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz" -O ; \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz.sha256" -O ; \
      fi && \
      sha256sum -c "${COMPONENT}.tar.gz.sha256" && \
      tar -zxf "${COMPONENT}.tar.gz" -C /opt/bitnami --strip-components=2 --no-same-owner --wildcards '*/files' && \
      rm -rf "${COMPONENT}".tar.gz{,.sha256} ; \
    done

RUN mkdir -p /out/kaniko/.docker /out/etc && cp /opt/bitnami/kaniko/bin/* /out/kaniko && cp /opt/bitnami/kaniko/nsswitch/nsswitch.conf /out/etc && chmod 775 /out/kaniko

######

FROM scratch

ARG TARGETARCH

ENV HOME="/root" \
    OS_ARCH="${TARGETARCH:-amd64}"

LABEL com.vmware.cp.artifact.flavor="sha256:59c774543cd827c5e74f2cc55d77e9e923fdcfe2487c135bbb41d7709b51eef6" \
      org.opencontainers.image.base.name="scratch" \
      org.opencontainers.image.created="2023-08-04T17:12:58Z" \
      org.opencontainers.image.description="Application packaged by VMware, Inc" \
      org.opencontainers.image.licenses="Apache-2.0" \
      org.opencontainers.image.ref.name="1.13.0-scratch-r3" \
      org.opencontainers.image.title="kaniko" \
      org.opencontainers.image.vendor="VMware, Inc." \
      org.opencontainers.image.version="1.13.0"

COPY prebuildfs /
COPY rootfs /
COPY --from=builder /out /

ENV APP_VERSION="1.13.0" \
    BITNAMI_APP_NAME="kaniko" \
    DOCKER_CONFIG="/kaniko/.docker" \
    DOCKER_CREDENTIAL_GCR_CONFIG="/kaniko/.config/gcloud/docker_credential_gcr_config.json" \
    PATH="/kaniko" \
    SSL_CERT_DIR="/etc/ssl/certs/" \
    USER="root"

WORKDIR /workspace

ENTRYPOINT [ "/kaniko/executor" ]

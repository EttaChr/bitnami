# Copyright VMware, Inc.
# SPDX-License-Identifier: APACHE-2.0

FROM docker.io/bitnami/minideb:bullseye

ARG TARGETARCH

LABEL com.vmware.cp.artifact.flavor="sha256:1e1b4657a77f0d47e9220f0c37b9bf7802581b93214fff7d1bd2364c8bf22e8e" \
      org.opencontainers.image.base.name="docker.io/bitnami/minideb:bullseye" \
      org.opencontainers.image.created="2023-08-07T01:27:30Z" \
      org.opencontainers.image.description="Application packaged by VMware, Inc" \
      org.opencontainers.image.licenses="Apache-2.0" \
      org.opencontainers.image.ref.name="0.67.0-debian-11-r29" \
      org.opencontainers.image.title="supabase-postgres-meta" \
      org.opencontainers.image.vendor="VMware, Inc." \
      org.opencontainers.image.version="0.67.0"

ENV HOME="/" \
    OS_ARCH="${TARGETARCH:-amd64}" \
    OS_FLAVOUR="debian-11" \
    OS_NAME="linux"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
# Install required system packages and dependencies
RUN install_packages ca-certificates curl libbz2-1.0 libcom-err2 libcrypt1 libffi7 libgcc-s1 libgssapi-krb5-2 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 liblzma5 libncursesw6 libnsl2 libreadline8 libsqlite3-0 libssl1.1 libstdc++6 libtinfo6 libtirpc3 procps zlib1g
RUN mkdir -p /tmp/bitnami/pkg/cache/ && cd /tmp/bitnami/pkg/cache/ && \
    COMPONENTS=( \
      "python-3.9.17-4-linux-${OS_ARCH}-debian-11" \
      "node-16.20.1-2-linux-${OS_ARCH}-debian-11" \
      "supabase-postgres-meta-0.67.0-1-linux-${OS_ARCH}-debian-11" \
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
RUN apt-get autoremove --purge -y curl && \
    apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives
RUN useradd -r -u 1001 -g root supabase-postgres-meta

RUN mkdir /.npm && chmod g+rwX /.npm
ENV APP_VERSION="0.67.0" \
    BITNAMI_APP_NAME="supabase-postgres-meta" \
    PATH="/opt/bitnami/python/bin:/opt/bitnami/node/bin:/opt/bitnami/supabase-postgres-meta/node_modules/.bin/:$PATH"

EXPOSE 3000

WORKDIR /opt/bitnami/supabase-postgres-meta
USER 1001
ENTRYPOINT [ "/opt/bitnami/node/bin/npm" ]
CMD [ "run", "start" ]

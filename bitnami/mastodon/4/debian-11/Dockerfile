# Copyright VMware, Inc.
# SPDX-License-Identifier: APACHE-2.0

FROM docker.io/bitnami/minideb:bullseye

ARG TARGETARCH

LABEL com.vmware.cp.artifact.flavor="sha256:1e1b4657a77f0d47e9220f0c37b9bf7802581b93214fff7d1bd2364c8bf22e8e" \
      org.opencontainers.image.base.name="docker.io/bitnami/minideb:bullseye" \
      org.opencontainers.image.created="2023-08-06T18:33:11Z" \
      org.opencontainers.image.description="Application packaged by VMware, Inc" \
      org.opencontainers.image.licenses="Apache-2.0" \
      org.opencontainers.image.ref.name="4.1.6-debian-11-r5" \
      org.opencontainers.image.title="mastodon" \
      org.opencontainers.image.vendor="VMware, Inc." \
      org.opencontainers.image.version="4.1.6"

ENV HOME="/" \
    OS_ARCH="${TARGETARCH:-amd64}" \
    OS_FLAVOUR="debian-11" \
    OS_NAME="linux"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
# Install required system packages and dependencies
RUN install_packages acl ca-certificates curl ffmpeg file imagemagick libbsd0 libbz2-1.0 libcom-err2 libcrypt1 libedit2 libffi7 libgcc-s1 libgmp10 libgnutls30 libgssapi-krb5-2 libhogweed6 libicu67 libidn11 libidn2-0 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 libldap-2.4-2 liblzma5 libmd0 libncursesw6 libnettle8 libnsl2 libp11-kit0 libpq5 libreadline-dev libreadline8 libsasl2-2 libsqlite3-0 libssl-dev libssl1.1 libstdc++6 libtasn1-6 libtinfo6 libtirpc3 libunistring2 libuuid1 libxml2 libxslt1.1 procps sqlite3 zlib1g
RUN mkdir -p /tmp/bitnami/pkg/cache/ && cd /tmp/bitnami/pkg/cache/ && \
    COMPONENTS=( \
      "python-3.9.17-4-linux-${OS_ARCH}-debian-11" \
      "wait-for-port-1.0.6-11-linux-${OS_ARCH}-debian-11" \
      "ruby-3.0.6-4-linux-${OS_ARCH}-debian-11" \
      "redis-client-7.0.12-1-linux-${OS_ARCH}-debian-11" \
      "postgresql-client-15.3.0-2-linux-${OS_ARCH}-debian-11" \
      "node-16.20.1-2-linux-${OS_ARCH}-debian-11" \
      "mastodon-4.1.6-0-linux-${OS_ARCH}-debian-11" \
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
RUN apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami

COPY rootfs /
RUN /opt/bitnami/scripts/mastodon/postunpack.sh
ENV APP_VERSION="4.1.6" \
    BITNAMI_APP_NAME="mastodon" \
    PATH="/opt/bitnami/python/bin:/opt/bitnami/common/bin:/opt/bitnami/ruby/bin:/opt/bitnami/redis/bin:/opt/bitnami/postgresql/bin:/opt/bitnami/node/bin:/opt/bitnami/mastodon/bin:$PATH"

EXPOSE 3000

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/mastodon/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/mastodon/run.sh" ]

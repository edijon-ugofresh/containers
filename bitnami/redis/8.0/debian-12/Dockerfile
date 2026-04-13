# Copyright Broadcom, Inc. All Rights Reserved.
# SPDX-License-Identifier: APACHE-2.0

FROM docker.io/library/debian:12-slim AS redis-builder

ARG REDIS_VERSION="8.0.3"
ARG REDIS_SHA256="33f37290b00b14e9a884dd4dcba335febd63ea16c51609d34fa41e031ad587df"
ARG REDIS_DOWNLOAD_URL="https://download.redis.io/releases/redis-${REDIS_VERSION}.tar.gz"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
RUN install_packages build-essential ca-certificates curl libssl-dev pkg-config
RUN mkdir -p /tmp/redis-source /opt/bitnami/redis/bin /opt/bitnami/redis/etc && \
    curl -fsSL "${REDIS_DOWNLOAD_URL}" -o /tmp/redis.tar.gz && \
    echo "${REDIS_SHA256}  /tmp/redis.tar.gz" | sha256sum -c - && \
    tar -xzf /tmp/redis.tar.gz -C /tmp/redis-source --strip-components=1 && \
    make -C /tmp/redis-source BUILD_TLS=yes -j"$(nproc)" && \
    install -m 0755 /tmp/redis-source/src/redis-benchmark /opt/bitnami/redis/bin/redis-benchmark && \
    install -m 0755 /tmp/redis-source/src/redis-check-aof /opt/bitnami/redis/bin/redis-check-aof && \
    install -m 0755 /tmp/redis-source/src/redis-check-rdb /opt/bitnami/redis/bin/redis-check-rdb && \
    install -m 0755 /tmp/redis-source/src/redis-cli /opt/bitnami/redis/bin/redis-cli && \
    install -m 0755 /tmp/redis-source/src/redis-server /opt/bitnami/redis/bin/redis-server && \
    install -m 0644 /tmp/redis-source/redis.conf /opt/bitnami/redis/etc/redis-default.conf && \
    rm -rf /tmp/redis-source /tmp/redis.tar.gz

FROM docker.io/library/debian:12-slim

ARG REDIS_VERSION="8.0.3"

LABEL org.opencontainers.image.base.name="docker.io/library/debian:12-slim" \
      org.opencontainers.image.description="Redis container image maintained from upstream Redis source releases." \
      org.opencontainers.image.documentation="https://github.com/edijon-ugofresh/containers/tree/main/bitnami/redis/README.md" \
      org.opencontainers.image.ref.name="${REDIS_VERSION}-debian-12" \
      org.opencontainers.image.source="https://github.com/edijon-ugofresh/containers/tree/main/bitnami/redis" \
      org.opencontainers.image.title="redis" \
      org.opencontainers.image.vendor="edijon-ugofresh" \
      org.opencontainers.image.version="${REDIS_VERSION}"

ENV HOME="/"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
RUN install_packages ca-certificates libgcc-s1 libssl3 libstdc++6 procps
COPY --from=redis-builder /opt/bitnami/redis /opt/bitnami/redis
RUN apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami
RUN find / -perm /6000 -type f -exec chmod a-s {} \; || true
RUN ln -s /opt/bitnami/scripts/redis/entrypoint.sh /entrypoint.sh
RUN ln -s /opt/bitnami/scripts/redis/run.sh /run.sh

COPY rootfs /
RUN /opt/bitnami/scripts/redis/postunpack.sh
ENV APP_VERSION="${REDIS_VERSION}" \
    BITNAMI_APP_NAME="redis" \
    PATH="/opt/bitnami/common/bin:/opt/bitnami/redis/bin:$PATH"

EXPOSE 6379

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/redis/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/redis/run.sh" ]

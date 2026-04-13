# Copyright Broadcom, Inc. All Rights Reserved.
# SPDX-License-Identifier: APACHE-2.0

FROM docker.io/library/debian:12-slim AS redis-sentinel-builder

ARG REDIS_VERSION="7.4.3"
ARG REDIS_SHA256="e1807d7c0f824f4c5450244ef50c1e596b8d09b35d03a83f4e018fb7316acf45"
ARG REDIS_DOWNLOAD_URL="https://download.redis.io/releases/redis-${REDIS_VERSION}.tar.gz"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
RUN install_packages build-essential ca-certificates curl libssl-dev pkg-config
RUN mkdir -p /tmp/redis-source /opt/bitnami/redis-sentinel/bin /opt/bitnami/redis-sentinel/etc && \
    curl -fsSL "${REDIS_DOWNLOAD_URL}" -o /tmp/redis.tar.gz && \
    echo "${REDIS_SHA256}  /tmp/redis.tar.gz" | sha256sum -c - && \
    tar -xzf /tmp/redis.tar.gz -C /tmp/redis-source --strip-components=1 && \
    make -C /tmp/redis-source BUILD_TLS=yes -j"$(nproc)" && \
    install -m 0755 /tmp/redis-source/src/redis-cli /opt/bitnami/redis-sentinel/bin/redis-cli && \
    install -m 0755 /tmp/redis-source/src/redis-server /opt/bitnami/redis-sentinel/bin/redis-server && \
    install -m 0755 /tmp/redis-source/src/redis-benchmark /opt/bitnami/redis-sentinel/bin/redis-benchmark && \
    install -m 0755 /tmp/redis-source/src/redis-check-aof /opt/bitnami/redis-sentinel/bin/redis-check-aof && \
    install -m 0755 /tmp/redis-source/src/redis-check-rdb /opt/bitnami/redis-sentinel/bin/redis-check-rdb && \
    if [[ -f /tmp/redis-source/src/redis-sentinel ]]; then \
        install -m 0755 /tmp/redis-source/src/redis-sentinel /opt/bitnami/redis-sentinel/bin/redis-sentinel; \
    else \
        ln -s redis-server /opt/bitnami/redis-sentinel/bin/redis-sentinel; \
    fi && \
    install -m 0644 /tmp/redis-source/sentinel.conf /opt/bitnami/redis-sentinel/etc/sentinel.conf && \
    rm -rf /tmp/redis-source /tmp/redis.tar.gz

FROM docker.io/library/debian:12-slim

ARG REDIS_VERSION="7.4.3"

LABEL org.opencontainers.image.base.name="docker.io/library/debian:12-slim" \
      org.opencontainers.image.description="Redis Sentinel container image maintained from upstream Redis source releases." \
      org.opencontainers.image.documentation="https://github.com/edijon-ugofresh/containers/tree/main/bitnami/redis-sentinel/README.md" \
      org.opencontainers.image.ref.name="${REDIS_VERSION}-debian-12" \
      org.opencontainers.image.source="https://github.com/edijon-ugofresh/containers/tree/main/bitnami/redis-sentinel" \
      org.opencontainers.image.title="redis-sentinel" \
      org.opencontainers.image.vendor="edijon-ugofresh" \
      org.opencontainers.image.version="${REDIS_VERSION}"

ENV HOME="/" \
    PATH="/opt/bitnami/redis-sentinel/bin:$PATH"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
RUN install_packages ca-certificates libgcc-s1 libssl3 libstdc++6 openssl procps
COPY --from=redis-sentinel-builder /opt/bitnami/redis-sentinel /opt/bitnami/redis-sentinel
RUN apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami
RUN find / -perm /6000 -type f -exec chmod a-s {} \; || true

COPY rootfs /
RUN /opt/bitnami/scripts/redis-sentinel/postunpack.sh
ENV APP_VERSION="${REDIS_VERSION}" \
    BITNAMI_APP_NAME="redis-sentinel"

EXPOSE 26379

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/redis-sentinel/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/redis-sentinel/run.sh" ]

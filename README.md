# Container Definitions Fork

This repository is an independently maintained fork of the historical `bitnami/containers` layout.
The `bitnami/` path is kept to limit churn in existing build paths and automation, but this repository is not the official Bitnami catalog and should not be presented as such.

## Current scope

- Active maintenance currently focuses on `bitnami/redis` and `bitnami/redis-sentinel`.
- The goal is to keep these images buildable from auditable upstream sources instead of Bitnami binary artifacts.
- Existing runtime filesystem contracts are preserved where practical to reduce migration work.

## Maintained Redis images

- [Redis](bitnami/redis/README.md)
- [Redis Sentinel](bitnami/redis-sentinel/README.md)
- Redis maintenance notes: [bitnami/redis/MAINTAINING.md](bitnami/redis/MAINTAINING.md)

## Build locally

```bash
docker build -t local/redis:7.4 bitnami/redis/7.4/debian-12
docker build -t local/redis:8.0 bitnami/redis/8.0/debian-12
docker build -t local/redis-sentinel:7.4 bitnami/redis-sentinel/7.4/debian-12
docker build -t local/redis-sentinel:8.0 bitnami/redis-sentinel/8.0/debian-12
```

## Licensing And Trademarks

- The packaging and repository code in this fork are licensed under Apache-2.0 unless noted otherwise. See [LICENSE.md](LICENSE.md).
- Upstream software shipped inside an image keeps its own license terms.
- Redis licensing depends on the upstream version. Review the official Redis licensing page before redistributing images or publishing derived work: [Redis licenses](https://redis.io/legal/licenses/).
- Redis is a registered trademark of Redis Ltd. References in this repository identify the upstream software only and do not imply sponsorship or endorsement.

## Compatibility Note

Some directories outside the Redis scope may still reflect upstream history and wording from the original source repository. Unless stated otherwise, that does not mean they are actively maintained in this fork.

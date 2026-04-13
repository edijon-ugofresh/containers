# Redis Container Image

This directory contains the Redis image definition maintained in this fork.
The historical `bitnami/redis` path is preserved for compatibility with the upstream repository layout, but the resulting image is not an official Bitnami image.

## Scope

- Build Redis from official upstream release tarballs.
- Keep the vendored runtime scripts already present in this repository.
- Preserve the existing filesystem layout under `/opt/bitnami/redis` and `/bitnami/redis` to reduce migration risk.
- Avoid Bitnami binary artifacts for the maintained Redis lines.

## Maintained versions

- [Redis 7.4 assets](7.4/README.md)
- [Redis 8.0 Dockerfile](8.0/debian-12/Dockerfile)

Operational details for this fork live in [MAINTAINING.md](MAINTAINING.md).

## Build

```bash
docker build -t local/redis:7.4 bitnami/redis/7.4/debian-12
docker build -t local/redis:8.0 bitnami/redis/8.0/debian-12
```

## Run

These examples are intended for local development only.

```bash
docker run --rm --name redis \
  -e ALLOW_EMPTY_PASSWORD=yes \
  -p 6379:6379 \
  local/redis:8.0
```

With persistence:

```bash
docker run --rm --name redis \
  -e ALLOW_EMPTY_PASSWORD=yes \
  -v /path/to/redis-persistence:/bitnami/redis/data \
  -p 6379:6379 \
  local/redis:8.0
```

## Compatibility

This fork keeps the existing container contract so current automation can continue to rely on:

- `/opt/bitnami/redis`
- `/bitnami/redis`
- environment variables such as `ALLOW_EMPTY_PASSWORD`, `REDIS_PASSWORD`, `REDIS_AOF_ENABLED`, `REDIS_REPLICATION_MODE`, `REDIS_MASTER_HOST`, and `REDIS_TLS_*`

## Licensing And Trademarks

- The packaging code in this repository is Apache-2.0. See [LICENSE.md](../../LICENSE.md).
- The Redis binaries and source code inside the image are licensed by Redis according to the upstream version in use.
- Redis 7.4.x is offered upstream under `RSALv2` or `SSPLv1`.
- Redis 8.x is offered upstream under `RSALv2`, `SSPLv1`, or `AGPLv3`.
- Review the official version matrix before redistribution: [Redis licenses](https://redis.io/legal/licenses/).
- Redis is a registered trademark of Redis Ltd. It is referenced here only to identify the upstream software.

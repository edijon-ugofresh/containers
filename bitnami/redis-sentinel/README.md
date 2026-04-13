# Redis Sentinel Container Image

This directory contains the Redis Sentinel image definition maintained in this fork.
The historical `bitnami/redis-sentinel` path is preserved for compatibility with the upstream repository layout, but the resulting image is not an official Bitnami image.

## Scope

- Build Redis Sentinel from official upstream Redis release tarballs.
- Keep the vendored runtime scripts already present in this repository.
- Preserve the existing filesystem layout under `/opt/bitnami/redis-sentinel` and `/bitnami/redis-sentinel`.
- Avoid Bitnami binary artifacts for the maintained Sentinel lines.

## Maintained versions

- [Redis Sentinel 7.4 assets](7.4/README.md)
- [Redis Sentinel 8.0 Dockerfile](8.0/debian-12/Dockerfile)

Operational details for the Redis image family live in [bitnami/redis/MAINTAINING.md](../redis/MAINTAINING.md).

## Build

```bash
docker build -t local/redis-sentinel:7.4 bitnami/redis-sentinel/7.4/debian-12
docker build -t local/redis-sentinel:8.0 bitnami/redis-sentinel/8.0/debian-12
```

## Run

These examples are intended for local development only.

```bash
docker network create app-tier

docker run -d --name redis-server \
  --network app-tier \
  -e ALLOW_EMPTY_PASSWORD=yes \
  local/redis:8.0

docker run --rm --name redis-sentinel \
  --network app-tier \
  -e REDIS_MASTER_HOST=redis-server \
  local/redis-sentinel:8.0
```

## Compatibility

This fork keeps the existing container contract so current automation can continue to rely on:

- `/opt/bitnami/redis-sentinel`
- `/bitnami/redis-sentinel`
- environment variables such as `REDIS_MASTER_HOST`, `REDIS_MASTER_SET`, `REDIS_MASTER_PASSWORD`, `REDIS_SENTINEL_QUORUM`, `REDIS_SENTINEL_FAILOVER_TIMEOUT`, and `REDIS_SENTINEL_TLS_*`

## Licensing And Trademarks

- The packaging code in this repository is Apache-2.0. See [LICENSE.md](../../LICENSE.md).
- The Redis binaries and source code inside the image are licensed by Redis according to the upstream version in use.
- Redis Sentinel built from Redis 7.4.x follows the upstream `RSALv2` or `SSPLv1` licensing options.
- Redis Sentinel built from Redis 8.x follows the upstream `RSALv2`, `SSPLv1`, or `AGPLv3` licensing options.
- Review the official version matrix before redistribution: [Redis licenses](https://redis.io/legal/licenses/).
- Redis is a registered trademark of Redis Ltd. It is referenced here only to identify the upstream software.

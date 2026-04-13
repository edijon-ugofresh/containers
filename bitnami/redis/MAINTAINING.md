# Redis Images Maintenance

This fork keeps `redis` and `redis-sentinel` maintainable without Bitnami binary artifacts or base images.

## Ownership Boundary

- Keep the runtime shell logic already vendored in this repository under `rootfs` and `prebuildfs`.
- Build on top of the official Debian 12 slim base image.
- Build Redis binaries from the official Redis release tarballs.
- Verify every tarball with the checksum published by the official [`redis/redis-hashes`](https://github.com/redis/redis-hashes) repository.
- Do not reintroduce downloads from `downloads.bitnami.com/files/stacksmith`.

## What To Update For A New Redis Release

Each supported branch keeps its Redis release input directly in the Dockerfiles:

- `bitnami/redis/7.4/debian-12/Dockerfile`
- `bitnami/redis/8.0/debian-12/Dockerfile`
- `bitnami/redis-sentinel/7.4/debian-12/Dockerfile`
- `bitnami/redis-sentinel/8.0/debian-12/Dockerfile`

For a patch release, update the two matching files for that major line:

1. `ARG REDIS_VERSION`
2. `ARG REDIS_SHA256`
3. `org.opencontainers.image.ref.name` and `APP_VERSION` are derived automatically from `REDIS_VERSION`

Official source tarballs follow this pattern:

```text
https://download.redis.io/releases/redis-${REDIS_VERSION}.tar.gz
```

Official checksums are published in:

```text
https://github.com/redis/redis-hashes
```

## Why This Layout

- `redis` and `redis-sentinel` still expose the same Bitnami-style filesystem layout under `/opt/bitnami`.
- Existing init and runtime scripts remain usable.
- The only external inputs are now the official Debian base image plus the upstream Redis release and its published checksum.
- `wait-for-port` is vendored locally for `redis`, so replica startup no longer depends on a Bitnami package.

## Local Verification

Build the two images you changed from their version directories:

```bash
docker build -t local/redis:8.0 bitnami/redis/8.0/debian-12
docker build -t local/redis-sentinel:8.0 bitnami/redis-sentinel/8.0/debian-12
```

Smoke test the binaries:

```bash
docker run --rm local/redis:8.0 redis-server --version
docker run --rm local/redis:8.0 redis-cli --version
docker run --rm local/redis-sentinel:8.0 redis-cli --version
```

Smoke test the container entrypoints:

```bash
docker run --rm -e ALLOW_EMPTY_PASSWORD=yes local/redis:8.0
docker run --rm -e ALLOW_EMPTY_PASSWORD=yes -e REDIS_MASTER_HOST=redis local/redis-sentinel:8.0
```

## Residual Risk

Redis 8 introduced optional module-related build paths upstream. The current fork deliberately builds the core Redis and Sentinel binaries with TLS enabled and keeps the image scope limited to `redis` and `redis-sentinel`.

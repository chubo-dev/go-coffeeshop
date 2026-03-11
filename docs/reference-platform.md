# Reference Platform Edition

This repository now has a container-first deployment path for the Chubo
reference platform.

## First Deployable Slice

The first runnable reference-platform edition is:

- `product-api`
- `counter-api`
- `grpc-gw`
- `web`
- upstream dependencies:
  - `postgres`
  - `rabbitmq`

This keeps the original user-visible flow intact while avoiding the full worker
set (`barista`, `kitchen`) in the first pass.

## Published Images

The repository publishes multi-arch images to GHCR:

- `ghcr.io/chubo-dev/go-coffeeshop-product-api:<tag>`
- `ghcr.io/chubo-dev/go-coffeeshop-counter-api:<tag>`
- `ghcr.io/chubo-dev/go-coffeeshop-grpc-gw:<tag>`
- `ghcr.io/chubo-dev/go-coffeeshop-web:<tag>`

Tags are intended to be immutable release tags, not `latest`.

## Runtime Model

- application services run as containers
- service-to-service edges use OpenWonton Connect backed by OpenGyoza
- ingress is provided by the reference-platform Traefik pack
- Postgres and RabbitMQ use upstream images for the first pass

## Why This Path

The original Nomad jobs in `build/nomad/jobs/` fetch the repository at runtime
and execute `go run` on cluster nodes. That is not the right model for the
reference platform.

The reference-platform edition uses prebuilt container images instead, so the
deployment path is:

1. build once in CI
2. push immutable images to GHCR
3. deploy containers through `reference-platform`

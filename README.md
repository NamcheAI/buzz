# Namche Buzz

This repository builds and deploys an unmodified, commit-pinned checkout of
[`block/buzz`](https://github.com/block/buzz) into the Namche estate. It is an
independent deployment wrapper, not a source fork: upstream code is neither
vendored nor patched here.

## Upstream pin

[`upstream.lock`](upstream.lock) is the entire source-selection interface. It
contains the upstream repository and a full Git commit SHA. CI validates both,
checks out that exact commit, verifies `HEAD`, and builds upstream's own
`Dockerfile` for `linux/amd64`.

To update Buzz, change the commit in `upstream.lock` in a pull request. Include
the upstream comparison or release notes in the PR description. Never point a
deployment at a branch or mutable image tag.

## Release contract

The release flow intentionally matches the other Namche applications:

1. Pull requests build the pinned upstream image without publishing it.
2. A squash merge to `main` builds the same pin, publishes an immutable GHCR
   digest, and dispatches that digest to `NamcheAI/infra` for staging.
3. A `v*` tag on that merged commit promotes the exact staged digest to
   production. Production never rebuilds.

This repository has no SSH key or tailnet credential. Its only deployment
credential is an environment-scoped token that can dispatch the allow-listed
`deploy-app.yml` workflow in `NamcheAI/infra`.

| Environment | URL | Trigger |
|---|---|---|
| staging | <https://buzz.8848.at> | merge to `main` |
| production | <https://buzz.namche.ai> | `v*` tag |

## Runtime state

PostgreSQL holds events and application metadata. Redis is the live shared
state service. Cloudflare R2 is the durable S3-compatible store for media and
Git objects. The relay's local Git directory is hydrate/cache scratch space;
stable relay and HMAC keys remain in 1Password and must not rotate during a
normal release.

The upstream image also bundles Buzz's invite and repository-browser web SPA.
It is not the full chat client; the primary end-user UI is the upstream desktop
application.

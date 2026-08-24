# HQ Spacecraft

Repository scaffold for researching self-hosted collaboration infrastructure
under an organization's own administrative control.

## What the source contains

- a root Compose file that includes storage, setup, service, and networking
  layers;
- directory boundaries for Gitea, n8n, Nextcloud, Wiki.js, search, Sentry,
  WireGuard, Traefik, DNS, and related services;
- one partial Pi-hole Compose definition; and
- placeholder Python utilities, a hello-world command, and a Taskfile greeting.

Most included Compose files and all backup/import/inspection utilities are
currently empty. The root composition therefore does not deploy the service set
named by the directory structure. Those names express the research inventory,
not implemented infrastructure.

## Intended role

HQ Spacecraft explores how collaboration, source hosting, automation,
knowledge, networking, monitoring, backup, and recovery could be assembled as
replaceable self-hosted services. It is not part of IPI consensus and is not
required to implement an IPI protocol specification.

Before any service profile becomes deployable, it needs pinned images,
configuration schemas, secret management, network exposure rules, health
checks, resource limits, backup/restore procedures, upgrade and rollback tests,
and a documented threat model.

## Development status

**Prototype scaffold.** Do not expose the current composition to the internet
or place sensitive data in it. The only root Task command currently prints a
greeting:

```sh
task
```

A future implemented profile should document its environment and provide a
Compose validation test plus a minimal end-to-end health check.

## Upstream and license

This work began from concepts and code in
[`Sarverott/infraforest`](https://github.com/Sarverott/infraforest). Attribution
is recorded in [`.github/NOTICE.md`](.github/NOTICE.md), and the repository is
distributed under the [MIT License](LICENSE).

Contributions follow the organization-wide contributing, security, and
governance policies in
[`ipicoin/.github`](https://github.com/ipicoin/.github).

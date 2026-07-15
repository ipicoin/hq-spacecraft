# HQ Spacecraft

An experimental, self-hosted service composition for organizations that want
to operate collaboration infrastructure under their own administrative control.

> **Status: pre-alpha.** This repository is an infrastructure design space, not
> a hardened distribution. Do not expose it to the internet or place sensitive
> data in it without reviewing every included service, image, secret, port, and
> storage path.

## Scope

The current Compose tree explores independently deployable building blocks for:

- source collaboration and automation with Gitea and n8n;
- files and knowledge with Nextcloud and Wiki.js;
- network access and routing with WireGuard, DNS, and Traefik;
- search, monitoring, security diagnostics, and supporting databases; and
- portable storage and import/export workflows.

The root [`compose.yaml`](compose.yaml) combines smaller definitions under
`src/` and `resources/`. Many definitions are incomplete and still require a
documented threat model, pinned images, secret management, health checks,
backup and restore tests, and upgrade procedures.

## Development

Prerequisites depend on the service being evaluated. At minimum, inspect the
Compose includes and install a current Docker Compose implementation before
running anything.

The current Taskfile only verifies Task itself is available:

```sh
task
```

Before proposing a deployable profile, document its intended environment,
network exposure, data classification, resource requirements, configuration,
backup plan, and reproducible verification steps.

## Relationship to IPI

HQ Spacecraft is an incubating operations project. It is not part of the IPI
consensus protocol and is not required to implement an IPI specification.
Interoperability or compliance claims must identify the relevant IPI proposal
and public verification evidence.

## Upstream and license

This work began from concepts and code in
[`Sarverott/infraforest`](https://github.com/Sarverott/infraforest). Upstream
and IPI changes are distributed under the [MIT License](LICENSE). Attribution
details are recorded in [`.github/NOTICE.md`](.github/NOTICE.md).

Contributions follow the organization-wide contributing, security, and
governance policies in [`ipicoin/.github`](https://github.com/ipicoin/.github).

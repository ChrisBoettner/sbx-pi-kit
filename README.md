# sbx-pi-kit

A [Docker Sandbox (sbx)](https://docs.docker.com/ai/sandboxes/) kit configuration (v2 schema) for the **pi coding agent**.

> **Note:** Docker Sandbox kits are an experimental feature. The kit file format and `sbx` CLI are still subject to change — double-check commands against `sbx run --help` if this README becomes out of date.

## Overview

This kit runs the pi coding agent (`@earendil-works/pi-coding-agent`) from the pre-baked `docker.io/sbx/pi-image` image, with `pi` as the container entrypoint. It is based on the [v2 schema](https://docs.docker.com/ai/sandboxes/customize/kits-v2/) for kit declaration.

The image is `docker/sandbox-templates:shell-docker` plus `fd-find` and pi. It is rebuilt nightly against pi's latest release by [docker/sbx-kits-contrib](https://github.com/docker/sbx-kits-contrib/tree/main/pi), so nothing is installed when a sandbox is created.

The kit declares no credentials and no model provider. Bring your own pi config, see [Customising the pi config](#customising-the-pi-config).

## Networking

By default, the kit allows outbound access to:

- pi.dev

Add the hosts your setup needs to `permissions.network.allow` in `spec.yaml`, or allow them with `sbx policy allow network <host>`. Typical additions:

- your model provider's API host
- `registry.npmjs.org`, if you use `pi install npm:...` or `pi update`
- `localhost:<port>`, for a model server running on the host (reach it as `host.docker.internal:<port>` from inside the sandbox)

## Requirements

- `sbx >= 0.42`

## Entrypoint

The container starts with `pi` as its entrypoint, so the pi coding agent is the default command when the sandbox boots.

## Usage

### Running with `sbx`

This is a sandbox kit, so its path takes the place of the agent name in `sbx run`.

Git clone this repo and run

```sh
# Run sandboxed pi in the current directory
sbx run /path/to/sbx-pi-kit

# Run pi in a specific workspace
sbx run /path/to/sbx-pi-kit /path/to/workspace

# Run pi in a specific workspace with additional directories mounted read-only
sbx run /path/to/sbx-pi-kit /path/to/workspace /path/to/extra/dir1:ro /path/to/extra/dir2:ro
```

A sandbox keeps the kit it was created with. To apply kit changes, remove the sandbox with `sbx rm <name>` and run it again.

For additional information on sbx, check the [Docker Sandbox Documentation](https://docs.docker.com/ai/sandboxes/).

### Customising the pi config

To bring your own pi setup (skills, models, extensions, config):

1. Create a `files/home/.pi/agent/` directory in this kit.
2. Populate it with your `models.json`, `settings.json`, `AGENTS.md`, `extensions/`, etc.
3. For skills installed under `.agents/`, add a `files/home/.agents/skills/` directory as well.

Everything under `files/home/` is copied to `/home/agent/` in the VM at provision time. Similarly, everything under `files/workspace/` gets copied to the workspace.

Check the kit after editing it:

```sh
sbx kit validate /path/to/sbx-pi-kit
```

## Version pinning

The kit uses `docker.io/sbx/pi-image:latest`, which is pulled again whenever a sandbox is created, so a new sandbox gets the current pi release. If you need a specific build, pin it in `spec.yaml` by replacing `latest` with one of the dated [image tags](https://hub.docker.com/r/sbx/pi-image/tags) (`YYYYMMDD-<commit>`).

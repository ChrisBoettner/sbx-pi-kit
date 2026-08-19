# sbx-pi-kit

A [Docker Sandbox (sbx) template](https://docs.docker.com/ai/sandboxes/) kit configuration (v2 schema) for the **pi coding agent**.

> **Note:** Docker Sandbox kits are an Early Access feature. The kit file format and `sbx` CLI are still subject to change — double-check commands against `sbx run --help` if this README becomes out of date.

## Overview

This kit builds on the `docker/sandbox-templates:shell-docker` base image and installs the pi coding agent (`@earendil-works/pi-coding-agent`) as the container entrypoint. It is based on the [v2 schema](https://docs.docker.com/ai/sandboxes/customize/kit-reference/) for kit declaration.

If no Docker engine is required inside the VM, change the base image to `docker/sandbox-templates:shell`.

## Networking

By default, the sandbox allows outbound access to:

- download.docker.com
- archive.ubuntu.com
- ports.ubuntu.com
- pi.dev
- security.ubuntu.com
- *.npmjs.org

These are needed in order to install `fd-find` and `pi`.

## Setup

Two setup steps run at provision time (both as `root`):

1. **Install `fd-find`** — soft requirement of pi, symlinked to `/usr/local/bin/fd`.
2. **Install pi** — the latest `@earendil-works/pi-coding-agent` npm package (with `--ignore-scripts`).

## Requirements

- `sbx >= 0.36`

## Entrypoint

The container starts with `pi` as its entrypoint, so the pi coding agent is the default command when the sandbox boots.

## Usage

### Running with `sbx`

Kits are attached to a sandbox with the `--kit` flag, followed by the agent name (defined through the name field in the kits `spec.yaml`).

Git clone this repo and run

```sh
# Run sandboxed pi in the current directory
sbx run --kit /path/to/sbx-pi-kit pi

# Run pi in a specific workspace
sbx run --kit /path/to/sbx-pi-kit pi /path/to/workspace

# Run pi in a specific workspace with additional directories mounted read-only
sbx run --kit /path/to/sbx-pi-kit pi /path/to/workspace /path/to/extra/dir1:ro /path/to/extra/dir2:ro
```



For additional information on sbx, check the [Docker Sandbox Documentation](https://docs.docker.com/ai/sandboxes/).

### Customising the pi config

To bring your own pi setup (skills, models, extensions, config):

1. Create a `files/home/.pi/` directory in this kit.
2. Populate it with your `model.json`, `config.yaml`, extensions, etc.
3. For skills installed under `.agents/`, add a `files/home/.agents/` directory as well.

Everything under `files/home/` is copied to `/home/agent/` in the VM at provision time. Similarly, everything under `files/workspace/` gets copied to the workspace.

## Version pinning

The setup uses the latest `@earendil-works/pi-coding-agent@latest`. If you need a specific version, pin it in `spec.yaml` by replacing `@latest` with a version tag (e.g. `@1.2.3`). The same is true for the base Docker image.
# ubuntu (image-based)

A minimal Dev Container configuration based on Ubuntu, using the `image` mode.

---

## Why no Dockerfile?

In `image` mode, you point directly to an existing image on Docker Hub (or any registry). There is nothing to build — VS Code pulls the image and starts the container immediately.

This is the simplest possible Dev Container setup. If you don't need to customize the environment, there is no reason to write a Dockerfile.

---

## Why `ubuntu:latest`?

Ubuntu is the most general-purpose Linux distribution available as a base image. It ships with a wide range of tools pre-installed and uses bash as its default shell, making it immediately familiar to most developers.

`latest` is used intentionally here. This configuration is meant for quick, disposable development environments where always having the newest Ubuntu feels natural — like working on a machine that keeps itself up to date.

If you need a stable, reproducible environment, pin to a specific LTS version such as `ubuntu:24.04` instead.

---

## How is this different from `dockerfile-based`?

| | `image-based` | `dockerfile-based` |
|---|---|---|
| Requires a Dockerfile | No | Yes |
| Can customize the environment | No | Yes |
| Startup speed | Faster | Slightly slower (build step) |
| Best for | Quick starts, standard environments | Custom tools, specific dependencies |

If you find yourself wanting to install extra tools or change the base image behavior, that's a sign you should switch to `dockerfile-based`.

---

## Why will your code appear under `/workspaces`?

Same reason as in `dockerfile-based` mode: VS Code automatically bind mounts your local project directory into the container at `/workspaces/<your-project-name>/` whenever it is in control of container creation.

See the `dockerfile-based/dead-simple` README for a full explanation of this behavior and how to override it.
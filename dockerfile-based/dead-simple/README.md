# dead-simple (dockerfile-based)

A minimal Dev Container configuration based on Alpine Linux, using the `dockerFile` mode.

---

## Why is this configuration so simple?

Because VS Code Dev Containers does a lot of heavy lifting for you automatically when you use `dockerFile` or `image` mode.

With just a `devcontainer.json` pointing to a `Dockerfile`, VS Code will:
- Build the image from your Dockerfile
- Create and start the container
- Mount your project directory into the container
- Open the workspace inside the container

You don't need to configure any volumes, bind mounts, or working directories manually.

---

## Why will your code always appear under `/workspaces`?

This is VS Code's **built-in automatic behavior** in `dockerFile` (and `image`) mode.

When VS Code takes control of container creation, it automatically bind mounts your local project directory into the container at:

```
/workspaces/<your-project-name>/
```

This is not a copy — it is a **bind mount**. Any changes made inside the container are immediately reflected on the host machine, and vice versa.

---

## Why is the directory named after your project?

VS Code uses your repository or folder name as the subdirectory name under `/workspaces/`. For example, if your project folder is called `my-app`, your code will appear at `/workspaces/my-app/`.

This happens automatically and requires no configuration on your part.

---

## What if you don't want this default behavior?

If you want full control over where your code lives inside the container — for example, to use a named Docker volume instead of a bind mount — you can override this behavior using the `workspaceMount` property in `devcontainer.json`:

```json
{
    "dockerFile": "Dockerfile",
    "workspaceMount": "source=my-volume,target=/workspaces,type=volume",
    "workspaceFolder": "/workspaces/my-app"
}
```

This tells VS Code: "Don't do your default bind mount. Use my volume instead."

---

## Why doesn't Docker Compose mode have this automatic behavior?

Because in `dockerComposeFile` mode, VS Code **hands over container creation entirely to Docker Compose**. It no longer controls how the container is created, so it cannot inject its automatic workspace mount.

Whatever ends up inside the container is completely determined by your `compose.yaml`. If you don't define a volume mount pointing to your source code, your code simply won't be there.

This is a deliberate trade-off: Docker Compose mode gives you full control, but removes the convenience of automatic mounting.

---

## About Alpine and its shell

Alpine Linux uses **BusyBox ash** as its default shell (`/bin/sh`), not bash or dash. This is a lightweight POSIX-compatible shell with limited support for bash-specific syntax such as arrays and `[[...]]` expressions.

If your scripts rely on bash features, either install bash explicitly in your Dockerfile:

```dockerfile
FROM alpine
RUN apk add --no-cache bash
```

Or switch to an image that ships with bash by default, such as `ubuntu`.

---

## References

- [Dev Containers: devcontainer.json reference](https://containers.dev/implementors/json_reference/)
- [VS Code: Create a Dev Container](https://code.visualstudio.com/docs/devcontainers/create-dev-container)
- [VS Code: devcontainer.json `workspaceMount`](https://code.visualstudio.com/remote/advancedcontainers/change-default-source-mount)
- [BusyBox ash documentation](https://busybox.net/downloads/BusyBox.html)
# hanhanmap-frontend 开发容器

## 为什么会有这个项目

在使用 Docker Compose 配置 VSCode 开发容器时，如果采用**纯 volume 挂载**的方式（即不使用 bind mount，而是让代码存放在 Docker named volume 中），会遇到一个根本性的问题：

> **VSCode 不会自动将本地工作文件夹中的代码同步到 volume 里。**

这意味着容器启动后，volume 是空的，开发环境无法直接使用。

使用 bind mount 虽然可以绕过这个问题，但在某些场景下（如跨平台性能、隔离性要求），纯 volume 方案更合适。因此，需要一个机制来完成"本地代码 → volume"这一步。

---

## 解决思路

### 1. 用 `code-sync` 完成代码同步

引入 `wulicoco/code-sync` 镜像作为初始化容器。它在开发容器创建时运行一次，将宿主机源代码从 `/source` 同步到 `/workspaces`（即共享 volume）中，之后自动退出。

### 2. 用 DooD 让容器内可以调用 Docker

为了在 `onCreateCommand` 中触发 `code-sync`，需要在开发容器内执行 `docker compose` 命令。这通过 **Docker outside of Docker（DooD）** 实现——将宿主机的 Docker socket 挂载进容器，容器内的 Docker CLI 实际操作的是宿主机的 Docker daemon。

这一切通过 devcontainer feature 一行配置完成，无需手动修改 `Dockerfile` 或 `compose.yaml`。

---

## 文件结构

```
.devcontainer/
├── Dockerfile          # 极简基础镜像，工具链由 features 负责安装
├── compose.yaml        # 定义开发容器和 code-sync 两个 service
└── devcontainer.json   # devcontainer 核心配置
```

---

## 启动流程

```
VSCode 触发 Rebuild Container
        ↓
Docker Compose 启动 hanhanmap-frontend 容器
        ↓
onCreateCommand 执行（DooD）
        ↓
宿主机 Docker daemon 运行 code-sync
        ↓
code-sync 将宿主机源代码同步到 volume-of-hanhan
        ↓
VSCode 连接容器，/workspace/frontend 已有代码
```

---

## 已知问题 / 待确认

### `onCreateCommand` 中路径解析的语境

`onCreateCommand` 执行的是：

```bash
docker compose -f .devconteline/compose.yaml run --rm code-sync
```

在 DooD 场景下，`-f .devcontainer/compose.yaml` 的路径能够正常解析并找到文件。但这个路径究竟是在**容器内**解析，还是在**宿主机**语境下解析，目前尚无官方文档说明。

实测可以正常运行，但底层机制尚不明确。已向官方提交 issue：

> [What is the path resolution context of `onCreateCommand` when using Docker outside of Docker (DooD)? · devcontainers/cli #1166](https://github.com/devcontainers/cli/issues/1166)

---

## 安全说明

DooD 将宿主机的 Docker socket 暴露给容器，容器内的 root 权限本质上等同于宿主机 root。这是 DooD 方案固有的安全风险，与 feature 版本无关，使用前请知悉。
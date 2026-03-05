# hanhanmap-frontend

前端开发环境，基于 VS Code Dev Containers。

## 环境

- Ubuntu (latest)
- Node.js (latest，由 fnm 管理)
- pnpm (latest)

## 前置条件

- VS Code
- Dev Containers 扩展
- Docker

## 启动

用 VS Code 打开项目目录，然后执行 **Reopen in Container**。

VS Code 会自动构建镜像并启动容器，完成后即可开始开发。

---

## Dockerfile 踩坑记录

### 1. `curl: not found`

**现象：** Docker build 失败，报 `/bin/sh: 1: curl: not found`。

**原因：** Ubuntu 最小镜像不自带 `curl`，而 fnm 安装脚本依赖它。

**解决：** 在 `apt-get install` 中显式加上 `curl`。

---

### 2. fnm 安装脚本静默失败

**现象：** Docker build 成功，但容器里 `fnm: command not found`，且 `/root/.local/share/fnm` 目录不存在。

**原因：** 同上，`curl` 未安装，管道中 `curl | bash` 的 bash 退出码为 0，Docker 不报错，安装脚本实际上从未执行。

**解决：** 同上，安装 `curl`。

---

### 3. `node: error while loading shared libraries: libatomic.so.1`

**现象：** fnm 和 node 均安装成功，但运行 `node` 时报共享库缺失。

**原因：** Ubuntu 最小镜像缺少 `libatomic1` 包。

**解决：** 在 `apt-get install` 中加上 `libatomic1`。

---

### 4. pnpm 安装后不可用

**现象：** 直接用 `corepack prepare pnpm@latest --activate` 安装 pnpm，结果不可用。

**原因：** fnm 安装的 Node.js 不附带 corepack。

**解决：** 改用 pnpm 官方提供的 Docker 专用安装命令：
```bash
wget -qO- https://get.pnpm.io/install.sh | ENV="$HOME/.bashrc" SHELL="$(which bash)" bash -
```
同时需要在 `apt-get install` 中加上 `wget`。
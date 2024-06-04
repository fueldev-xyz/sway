# 依赖

Forc 有一个依赖管理系统，可以使用 git 和 `ipfs` 拉取包。这允许用户构建和共享 Forc 库。

## 添加依赖

如果你的 `Forc.toml` 还没有 `[dependencies]` 表，添加一个。在下面列出包名及其来源。目前，`forc` 支持 `git`、`ipfs` 和 `path` 来源。

如果指定了 `git` 来源，`forc` 将获取给定 URL 的 git 仓库，然后在 git 仓库中的任何地方搜索具有给定名称的 `Forc.toml` 包。

以下示例添加了一个名为 `custom_lib` 的库依赖。对于 git 依赖，你可以选择指定 `branch`、`tag` 或 `rev`（即提交哈希）引用。

```toml
[dependencies]
custom_lib = { git = "https://github.com/FuelLabs/custom_lib", branch = "master" }

# custom_lib = { git = "https://github.com/FuelLabs/custom_lib", tag = "v0.0.1" }

# custom_lib = { git = "https://github.com/FuelLabs/custom_lib", rev = "87f80bdf323e2d64e213895d0a639ad468f4deff" }

```

使用 `path` 依赖本地库：

```toml
[dependencies]
custom_lib = { path = "../custom_lib" }
```

对于 `ipfs` 来源，`forc` 将使用本地 `ipfs` 节点或公共网关获取指定的 `cid`。`forc` 会自动尝试连接本地 `ipfs` 节点。如果失败，则默认使用 `https://ipfs.io/` 作为网关。

以下示例添加了一个 `ipfs` 来源的依赖。

```toml
[dependencies]
custom_lib = { ipfs = "QmYwAPJzv5CZsnA625s3Xf2nemtYgPpHdWEz79ojWnPbdG" }
```

一旦包被添加，运行 `forc build` 将自动下载添加的依赖。

## 更新依赖

要更新 Forc 目录中的依赖，可以运行 `forc update`。对于 `path` 和 `ipfs` 依赖，这将没有效果。对于具有 `branch` 引用的 `git` 依赖，这将更新项目以使用给定分支的最新提交。

### 感觉跳转加群有点流氓行为 改成部署成功提醒
<img width="1312" height="750" alt="image" src="https://github.com/user-attachments/assets/17ab8bc8-9bc3-44d8-8f44-bc5906ed7347" />
想加群的自己点击添加吧 tg交流群 https://t.me/+ft-zI76oovgwNmRh 


###  配套工具

| 类型 | 描述 | 链接 |
| :--- | :--- | :--- |
|  **生成节点** | 一键生成所有优选 IP/域名的 VLESS 链接 | [https://cfy.jhb.ovh/](https://cfy.jhb.ovh/) |
|  **文字教程** | 详细的部署与使用说明博客文章 | [https://joeyblog.net/yuanchuang/1146.html](https://joeyblog.net/yuanchuang/1146.html) |
|  **视频教程** | 直观的操作演示和功能讲解 | https://youtu.be/AA2sSVt9x5s |

###  部署


| 变量名 | 值 | 说明 |
| :--- | :--- | :--- |
| `u` | `你的 UUID` | **必需**。 |
| `p` | `proxyip` | **可选**但强烈推荐，填写一个稳定的用来访问 Cloudflare IP 可以用 bpb.yousef.isegaro.com。 |
| `s` | `你的SOCKS5地址` | **可选**。用于将所有出站流量通过 SOCKS5 代理转发，格式为 `user:pass@host:port` 或 `host:port`。 |

###  致谢

  * 本项目基于 [zizifn/edgetunnel](https://github.com/zizifn/edgetunnel) 修改，感谢原作者的贡献。

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=byJoey/cfnew&type=Timeline)](https://www.star-history.com/#byJoey/cfnew&Timeline&LogScale)


# JoeyNews Worker 自动同步与部署

[![Sync Worker Status](https://github.com/pusws/joeynews/actions/workflows/sync_worker.yml/badge.svg)](https://github.com/pusws/joeynews/actions/workflows/sync_worker.yml)

## 项目简介

此项目旨在全自动同步上游仓库 [byJoey/cfnew](https://github.com/byJoey/cfnew) 中的 Worker 脚本，并将其自动部署到本人的 Cloudflare Workers 账户。

项目搭建了一套完整的自动化流水线，只需进行一次性设置，之后便无需任何手动干预，即可保持 Cloudflare Worker 与上游源代码的同步，并持久化管理所需的环境变量。

## 核心功能

- **🕒 定时同步**：通过 GitHub Actions，每日定时检查并拉取上游仓库的最新代码。
- **📦 自动构建**：自动将拉取的源文件处理成 Cloudflare Worker 所需的 `_worker.js` 文件，并额外生成一个 `worker.zip` 压缩包。
- **🚀 自动部署**：当代码同步到本仓库后，会自动触发 Cloudflare 的 Git 集成功能，将最新的 Worker 版本部署到全球边缘网络。
- **🔑 变量持久化**：支持通过两种不同方式管理 Worker 所需的 UUID 等变量，确保变量在代码更新后不会丢失。
- **🔧 配置清晰**：通过 `wrangler.toml` 文件管理所有部署配置，易于理解和修改。

## 工作流程

本项目的全自动流程如下：

1.  **定时触发**
    - GitHub Actions 工作流 (`.github/workflows/sync_worker.yml`) 每日定时启动。

2.  **拉取源码**
    - 工作流访问上游仓库 `byJoey/cfnew`，下载最新的 `少年你相信光吗` 文件。

3.  **处理文件**
    - 将下载的文件重命名为 `_worker.js`。
    - 将 `_worker.js` 文件压缩为 `worker.zip`。

4.  **提交更新**
    - 工作流将更新后的 `_worker.js` 和 `worker.zip` 文件自动提交到本仓库的 `main` 分支。

5.  **触发部署**
    - 这次提交（push）操作被 Cloudflare Workers 的 Git 集成功能检测到。
    - Cloudflare 根据仓库根目录下的 `wrangler.toml` 配置文件，自动将 `_worker.js` 部署为新的 Worker 版本。Worker 在运行时会自动加载已配置的变量或密钥。

## 首次部署教程

这是一个**一次性**的设置流程，用于将此 GitHub 仓库与您的 Cloudflare Worker 服务连接起来。

### 准备工作
在开始之前，请确保：
- 您拥有一个 Cloudflare 账户。
- 本仓库已包含正确的 `.github/workflows/sync_worker.yml` 和 `wrangler.toml` 文件。
- "Sync Worker" 工作流至少已成功运行一次，确保仓库中已存在 `_worker.js` 文件。

### 部署步骤

1.  **登录并创建 Worker 服务**
    - 登录到您的 Cloudflare 仪表盘。
    - 在左侧菜单进入 **Workers & Pages**。
    - 点击 **创建应用程序 (Create application)** -> **Workers** 标签页。
    - 点击 **创建 Worker (Create Worker)**。
    - 为您的 Worker **命名**。 **注意**：此名称**必须**与 `wrangler.toml` 文件中的 `name` 字段完全一致（例如 `joeynews`）。
    - 点击 **部署 (Deploy)** 来创建一个空的 Worker 服务。

2.  **连接到 GitHub 仓库**
    - 部署成功后，进入该 Worker 的管理页面。
    - 点击 **部署 (Deployments)** 标签页。
    - 在页面上方找到并点击 **从 Git 部署 (Deploy from Git)** 或 **Connect Git**。
    - 按照提示授权并选择您这个 GitHub 仓库 (`pusws/joeynews`)。

3.  **配置并完成部署**
    - 在最后的配置页面，确认以下设置：
        - **生产分支**: `main`
        - **自动部署**: 启用
        - **wrangler.toml 文件路径**: `/wrangler.toml` (保持默认即可)
    - 点击 **保存并部署 (Save and Deploy)**。

Cloudflare 将会拉取您的仓库并进行首次部署。部署成功后，您的自动化流水线就正式激活了。

## 管理持久化变量 (UUID)

此 Worker 需要一个名为 `u` 的 UUID 变量才能正常工作。为确保此变量在每次自动部署后**不被重置或丢失**，您有两种方案可选。

---

### 方案一：在 `wrangler.toml` 中定义明文变量 (简单但不推荐)

此方案将变量直接写入配置文件，简单直接，但会将您的 UUID **公开**在 GitHub 仓库中。

**操作步骤:**
1.  编辑仓库根目录下的 `wrangler.toml` 文件。
2.  在文件末尾添加 `[vars]` 部分。

**`wrangler.toml` 配置示例:**
```toml
# wrangler.toml (方案一配置)

name = "joeynews"
main = "_worker.js"
compatibility_date = "2025-09-08"
no_bundle = true

[vars]
u = "5b89b269-f5e1-4b2e-a504-e730056f365f"
```

- **优点**:
    - 配置简单，所有信息都在一个文件里。
- **缺点**:
    - **安全性差**：UUID 以明文形式存储在公开仓库中，任何人都可以看到。

---

### 方案二：使用 Cloudflare Secrets (安全，强烈推荐)

此方案使用 Cloudflare 提供的密钥管理功能，将变量**加密存储**在 Cloudflare 端。变量会永久绑定到您的 Worker 服务，**不会**在 GitHub 上暴露，也**不会**被新的部署覆盖。

**操作步骤 (一次性设置):**
1.  **进入 Worker 设置**
    - 在 Cloudflare 仪表盘，进入您的 Worker (`joeynews`) 的管理页面。
    - 点击 **设置 (Settings)** 标签页。
    - 在左侧子菜单中选择 **变量 (Variables)**。

2.  **添加密钥 (Secret)**
    - 向下滚动到 **Worker 密钥 (Worker Secrets)** 部分。
    - 点击 **添加密钥 (Add Secret)**。
    - 填写以下信息：
        - **密钥名称 (Secret name)**: `u`
        - **密钥值 (Secret value)**: `5b89b269-f5e1-4b2e-a504-e730056f365f`
    - 点击 **保存 (Save)**。

**`wrangler.toml` 配置示例:**
当使用此方案时，您的 `wrangler.toml` 文件**不需要**包含任何变量信息。
```toml
# wrangler.toml (方案二配置)

name = "joeynews"
main = "_worker.js"
compatibility_date = "2025-09-08"
no_bundle = true
```

- **优点**:
    - **高度安全**：变量被加密，不会在代码仓库中暴露。
    - **一劳永逸**：设置一次后，变量将永久生效，不受代码更新影响。
- **缺点**:
    - 配置步骤比方案一略多。

> **结论**: 强烈建议使用**方案二 (Cloudflare Secrets)** 来管理您的 UUID 和任何其他敏感数据。

## 配置文件说明

-   **`.github/workflows/sync_worker.yml`**: 定义了 GitHub Actions 的自动化工作流。
-   **`wrangler.toml`**: Cloudflare Worker 的部署“说明书”。`no_bundle = true` 是此配置的关键，它指示 Cloudflare 直接上传脚本，从而解决了上游代码的兼容性问题。

## 鸣谢

本项目所使用的 Worker 脚本源码来自 [byJoey/cfnew](https://github.com/byJoey/cfnew) 仓库，感谢原作者的分享。

本项目所使用的 Worker 脚本源码来自 [byJoey/cfnew](https://github.com/byJoey/cfnew) 仓库，感谢原作者的分享。





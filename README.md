# Scoop 修改版（国内加速版）

一个基于 [ScoopInstaller/Scoop](https://github.com/ScoopInstaller/Scoop) 的修改分支，重点解决国内网络环境下安装慢、下载失败的问题，并做了一些精简与易用性改进。

---

## 目录

- [Scoop 是什么？](#scoop-是什么)
- [本修改版改了什么、怎么用](#本修改版改了什么怎么用)
- [环境要求](#环境要求)
- [安装](#安装)
- [快速上手](#快速上手)
- [配置国内加速](#配置国内加速)
- [命令别名](#命令别名)
- [常用配置一览](#常用配置一览)
- [常见问题](#常见问题)
- [相关链接](#相关链接)

---

## Scoop 是什么？

Scoop 是一个面向 Windows 的命令行软件包管理器，类似 macOS 的 Homebrew、Linux 的 apt/dnf。它用一条命令即可安装、更新、卸载软件，并带来这些好处：

- **无需管理员权限**：软件默认安装到用户目录（`~/scoop`），安装过程不弹 UAC、不写系统目录。
- **绿色便携**：绝大部分应用以绿色（便携）方式安装，卸载时直接删目录，不留注册表垃圾。
- **不污染 PATH**：Scoop 通过 `shims`（命令快捷入口）把可执行命令暴露到 PATH，软件本体保持独立，「装什么命令就有什么命令」。
- **一条命令完成全部操作**：搜索、安装、更新、卸载、清理、锁定版本。
- **多软件仓库（bucket）**：Scoop 自带 main 仓库，通过 `scoop bucket add` 可以挂载 extras、versions、java、nerd-fonts 等社区仓库。
- **版本管理**：可同时安装多个大版本（如 JDK 8/11/17），用 `scoop reset` 自由切换。

## 本修改版改了什么、怎么用

| 改进点 | 怎么用 | 详见 |
| --- | --- | --- |
| 一键安装本版本 | 直接运行本仓库的 `install.ps1`，无需先装官方版再换源 | [安装](#安装) |
| 默认源指向本项目 | 装好后正常执行 `scoop update`，更新的就是本修改版 | [安装](#安装) |
| GitHub / SourceForge 下载加速 | `scoop config github_mirror_url <前缀>`、`scoop config sourceforge_mirror_url <前缀>` | [配置国内加速](#配置国内加速) |
| bucket 浅克隆 | 默认自动生效，可通过 `BUCKET_GIT_DEPTH` 调整深度 | [常用配置一览](#常用配置一览) |
| 内置命令别名 | `scoop in`、`scoop rm`、`scoop se`、`scoop up` 直接可用 | [命令别名](#命令别名) |

各改进的具体用法见下方对应章节。

## 环境要求

- Windows 7 SP1 及以上（建议 Windows 10/11）
- PowerShell 5.1 及以上（推荐 PowerShell 7）
- Git（安装与更新时必需，可用 `winget install Git.Git` 安装，或装好 Scoop 后 `scoop install git`）

## 安装

### 方式一：直接安装本版本（推荐）

在 PowerShell 中运行本仓库的安装脚本：

```powershell
irm https://raw.githubusercontent.com/Adrts/Scoop-Adrts-Modified-Version/main/install.ps1 | iex
```

也可以先下载 `install.ps1`，再在本地执行：

```powershell
.\install.ps1
```

安装脚本支持可选参数，例如自定义目录和代理：

```powershell
.\install.ps1 -ScoopDir 'D:\scoop' -ScoopGlobalDir 'D:\scoop-global' -NoProxy
```

| 参数 | 说明 |
| --- | --- |
| `-ScoopDir` | Scoop 根目录，默认 `~/scoop` |
| `-ScoopGlobalDir` | 全局应用目录，默认 `C:\ProgramData\scoop` |
| `-ScoopCacheDir` | 下载缓存目录 |
| `-NoProxy` | 安装过程不使用系统代理 |
| `-Proxy` / `-ProxyCredential` / `-ProxyUseDefaultCredentials` | 指定代理 |
| `-RunAsAdmin` | 强制以管理员身份运行安装 |

### 方式二：已安装官方 Scoop，直接换源

```powershell
scoop config SCOOP_REPO https://github.com/Adrts/Scoop-Adrts-Modified-Version
scoop config SCOOP_BRANCH main
scoop update
```

`scoop update` 会把 Scoop 本体切换到本修改版；机器上若还没有 git，先执行 `scoop install git`。

## 快速上手

| 命令 | 说明 |
| --- | --- |
| `scoop search <关键词>` | 在所有 bucket 中搜索软件 |
| `scoop install <应用>` | 安装软件，如 `scoop install 7zip git`（可一次装多个） |
| `scoop install -g <应用>` | 全局安装（所有用户可用，需要管理员权限） |
| `scoop update` | 更新 Scoop 本体 + 全部 bucket |
| `scoop update *` | 更新所有已安装的软件 |
| `scoop update <应用>` | 更新指定软件 |
| `scoop status` | 查看哪些已安装软件有更新 |
| `scoop list` | 列出已安装软件（`scoop list <关键词>` 过滤） |
| `scoop uninstall <应用>` | 卸载软件（`-p` 连同用户配置一起删除） |
| `scoop info <应用>` | 查看软件详细信息（版本、依赖、主页等） |
| `scoop home <应用>` | 打开软件官网 |
| `scoop prefix <应用>` | 显示软件的安装目录 |
| `scoop which <命令>` | 查看某个命令由哪个软件提供 |
| `scoop cleanup <应用>` / `scoop cleanup *` | 清理旧版本，释放空间 |
| `scoop cache rm *` | 清空下载缓存 |
| `scoop hold <应用>` / `scoop unhold <应用>` | 锁定 / 解锁版本，防止被更新 |
| `scoop export` / `scoop import` | 导出 / 导入已安装清单（备份、迁移） |
| `scoop create <名称>` | 依据模板创建应用 manifest（JSON 清单） |
| `scoop cat <应用>` | 查看应用 manifest 内容 |
| `scoop depends <应用>` | 查看应用依赖 |
| `scoop checkup` | 检查 Scoop 环境健康状态 |
| `scoop help` | 查看全部命令列表 |

### 管理软件仓库（bucket）

```powershell
scoop bucket known         # 查看所有已知（可用）的 bucket 仓库
scoop bucket list          # 查看已添加的 bucket
scoop bucket add extras    # 添加社区仓库 extras（已知仓库可省略地址）
scoop bucket add versions  # 添加历史版本仓库
scoop bucket rm extras     # 移除仓库
```

常用社区 bucket：`extras`（大量 GUI 应用）、`versions`（历史/预览版本）、`java`（JDK/JRE 全家桶）、`nerd-fonts`（编程字体）、`nirsoft`、`sysinternals`、`nonportable`、`games`。这些也写在 `buckets.json` 中，`scoop bucket known` 会显示已知仓库列表。

## 配置国内加速

本修改版的两个核心加速配置项：

```powershell
# GitHub 下载加速：前缀会自动拼在 github.com 链接前面
scoop config github_mirror_url https://v6.gh-proxy.org

# SourceForge 下载加速
scoop config sourceforge_mirror_url https://v6.gh-proxy.org
```

原理：下载链接包含 `github.com` / `sourceforge.net` 时，自动改写为 `前缀 + 原始链接`，例如：

```
https://v6.gh-proxy.org/https://github.com/xxx/yyy/releases/download/v1.0.0/yyy.zip
```

常见加速前缀（可自行挑选稳定可用的）：

- `https://v6.gh-proxy.org`（gh-proxy v6，install.ps1 内置同款）
- `https://gh-proxy.com`
- `https://ghproxy.net`
- `https://ghfast.top`

取消加速、恢复直连：

```powershell
scoop config rm github_mirror_url
scoop config rm sourceforge_mirror_url
```

> 说明：镜像只作用于软件**下载**环节；Scoop 本体与 bucket 的 git 更新走 `SCOOP_REPO` / bucket 地址。如需给本体更新也加速，可把 `SCOOP_REPO` 配成带前缀的完整地址（前提是该镜像支持 git 克隆）。

## 命令别名

### 内置别名（本修改版新增）

| 别名 | 等价命令 |
| --- | --- |
| `scoop in <应用>` | `scoop install <应用>` |
| `scoop up` / `scoop up <应用>` | `scoop update` / `scoop update <应用>` |
| `scoop se <关键词>` | `scoop search <关键词>` |
| `scoop rm <应用>` / `scoop remove <应用>` | `scoop uninstall <应用>` |

### 自定义别名（上游自带功能）

```powershell
scoop alias add upgrade 'scoop update *' '升级所有软件'
scoop alias add rm 'scoop uninstall $args[0]' '卸载指定软件'
scoop alias list          # 列出所有自定义别名
scoop alias rm upgrade    # 删除别名
```

自定义别名存在 `ALIAS` 配置中，优先级高于内置别名。

## 常用配置一览

```powershell
scoop config                  # 查看所有配置
scoop config <键> <值>         # 设置配置
scoop config rm <键>           # 删除配置
```

| 配置键 | 说明 |
| --- | --- |
| `github_mirror_url` | GitHub 下载加速前缀（本修改版新增） |
| `sourceforge_mirror_url` | SourceForge 下载加速前缀（本修改版新增） |
| `BUCKET_GIT_DEPTH` | bucket 浅克隆深度，默认 10（本修改版新增） |
| `SCOOP_REPO` | Scoop 本体仓库，本版默认指向本项目 |
| `SCOOP_BRANCH` | Scoop 本体分支，本版默认 `main` |
| `proxy` | HTTP 代理，如 `scoop config proxy 127.0.0.1:7890` |
| `use_sqlite_cache` | 是否启用 SQLite 搜索缓存（`true` 搜索更快） |
| `last_update` | 上次更新时间（自动维护，一般不用动） |

## 常见问题

| 问题 | 解决办法 |
| --- | --- |
| 安装或下载超时、失败 | 配置 `github_mirror_url` / `sourceforge_mirror_url`，或配置 `proxy` |
| 提示需要 git | 先 `scoop install git`（或 `winget install Git.Git`） |
| `unable to verify the first certificate` | 执行 `scoop config rm proxy`（取消坏的代理设置）或配置正确的代理 |
| bucket 更新报错 / 合并冲突 | 本版已用 `fetch + reset` 代替 `pull`，一般不再出现；必要时 `scoop bucket rm <仓库>` 后重新 `add` |
| 想回到官方版 | `scoop config SCOOP_REPO https://github.com/ScoopInstaller/Scoop`、`scoop config SCOOP_BRANCH master`，再 `scoop update` |

## 相关链接

- 上游项目：[ScoopInstaller/Scoop](https://github.com/ScoopInstaller/Scoop)
- 官方安装脚本：[ScoopInstaller/Install](https://github.com/ScoopInstaller/Install)
- 常用 bucket：[Main](https://github.com/ScoopInstaller/Main)、[Extras](https://github.com/ScoopInstaller/Extras)、[Versions](https://github.com/ScoopInstaller/Versions)
- 使用指南：[Scoop Wiki](https://github.com/ScoopInstaller/Scoop/wiki)

## License

Scoop 及本修改版以 [Unlicense](LICENSE)（公有领域）协议发布。
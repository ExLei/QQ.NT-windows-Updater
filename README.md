# QQ NT Windows Versions

[![Release](https://github.com/ExLei/QQ.NT-windows-Updater/actions/workflows/update.yml/badge.svg)](https://github.com/ExLei/QQ.NT-windows-Updater/actions/workflows/update.yml)
![GitHub Downloads (all assets, all releases)](https://img.shields.io/github/downloads/ExLei/QQ.NT-windows-Updater/total)

自动追踪 [QQ NT (Windows)](https://im.qq.com/) 安装包版本，通过 GitHub Actions 每小时检查更新，自动发布 Release。

本仓库为通用版本追踪仓库，提供安装包、版本号、SHA256 哈希值等信息，可供任何包管理器（Scoop、Winget 等）或自动化工具使用。

## Release 内容

每个 Release 包含：

| 字段 | 说明 |
|------|------|
| **安装包** | `QQ_{版本号}_x64.exe`，从腾讯官方 CDN 下载 |
| **Version** | 版本号，如 `9.9.31.260528` |
| **Download URL** | 官方下载链接 |
| **SHA256** | 安装包 SHA256 哈希值（全小写） |
| **Last Modified** | 安装包在 CDN 上的最后修改时间 |

## 版本号格式

版本号从官方下载链接中提取，格式为 `{主版本}.{构建号}`：

```
QQ_9.9.31_260528_x64_01.exe
       ────── ──────
       主版本   构建号
            ↓
      9.9.31.260528
```

## 工作流

```
每小时触发 (cron: '0 * * * *')
  │
  ├─ 从腾讯 CDN 获取最新版本信息（轻量请求，不下载安装包）
  │
  ├─ 与本仓库最新 Release tag 比对版本
  │    ├─ 版本相同 → 结束 ✅
  │    └─ 版本不同 ↓
  │
  └─ 下载安装包 → 计算 SHA256 → 创建 GitHub Release
```

## 使用方式

### 通过 GitHub API 获取最新版本

```bash
# 获取最新 Release 信息
curl -s https://api.github.com/repos/ExLei/QQ.NT-windows-Updater/releases/latest

# 仅获取版本号
curl -s https://api.github.com/repos/ExLei/QQ.NT-windows-Updater/releases/latest | jq -r '.tag_name | ltrimstr("v")'

# 仅获取 SHA256
curl -s https://api.github.com/repos/ExLei/QQ.NT-windows-Updater/releases/latest | jq -r '.body' | grep 'SHA256' | awk '{print $2}'
```

### Scoop

```powershell
scoop bucket add exlei https://github.com/Exlei/Scoop-Bucket
scoop install exlei/Tencent.QQ.NT
```

### Winget

可基于本仓库的 Release 信息创建 Winget 清单，参考 [Winget 包规范](https://learn.microsoft.com/en-us/windows/package-manager/package/)。

## 免责声明

本项目仅用于版本追踪和校验，安装包版权归腾讯所有。所有安装包均从腾讯官方 CDN 下载，未做任何修改。

本项目基于 [MIT License](./LICENSE) 发布。安装包不属于本项目，归其各自所有者所有。本项目仅作存档用途，不对任何文件进行修改。

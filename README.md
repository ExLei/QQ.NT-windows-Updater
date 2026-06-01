# QQ NT Windows Versions

自动追踪 [QQ NT (Windows)](https://im.qq.com/) 安装包版本，通过 GitHub Actions 每小时检查更新，自动发布 Release 并同步 [Scoop Bucket](https://github.com/Exlei/Scoop-Bucket) 清单。

## Release 内容

每个 Release 包含：

| 字段 | 说明 |
|------|------|
| **安装包** | `QQ_{版本号}_x64.exe`，从腾讯官方 CDN 下载 |
| **Version** | Scoop 格式版本号，如 `9.9.31.260528` |
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
  ├─ 下载安装包 → 计算 SHA256 → 创建 GitHub Release
  │
  └─ 同步更新 Scoop-Bucket 中的 Tencent.QQ.NT.json
```

## Scoop 安装

```powershell
scoop bucket add exlei https://github.com/Exlei/Scoop-Bucket
scoop install exlei/Tencent.QQ.NT
```

## 相关项目

- [Exlei/Scoop-Bucket](https://github.com/Exlei/Scoop-Bucket) — Scoop 软件包仓库
- [cscnk52/wechat-windows-versions](https://github.com/cscnk52/wechat-windows-versions) — 微信 Windows 版本追踪（灵感来源）

## 免责声明

本项目仅用于版本追踪和校验，安装包版权归腾讯所有。所有安装包均从腾讯官方 CDN 下载，未做任何修改。

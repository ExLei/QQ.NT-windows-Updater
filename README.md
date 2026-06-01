# QQ NT Windows Versions

自动追踪 [QQ NT (Windows)](https://im.qq.com/) 安装包版本，通过 GitHub Actions 每小时检查更新，自动发布 Release。

本仓库为通用版本追踪仓库，提供安装包、多格式哈希值、结构化元数据，可供任何包管理器（Scoop、Winget、Chocolatey 等）或自动化工具使用。

## Release Assets

每个 Release 包含以下文件：

| 文件 | 说明 |
|------|------|
| `QQ_{版本号}_x64.exe` | QQ NT 安装包（从腾讯官方 CDN 下载） |
| `QQ_{版本号}_x64.exe.sha256` | SHA256 校验文件（兼容 `sha256sum -c`） |
| `QQ_{版本号}_x64.exe.sha1` | SHA1 校验文件（兼容 `sha1sum -c`） |
| `QQ_{版本号}_x64.exe.md5` | MD5 校验文件（兼容 `md5sum -c`） |
| `metadata.json` | 结构化元数据（JSON 格式） |

## metadata.json 格式

```json
{
  "version": "9.9.31.260528",
  "url": "https://qqdl.gtimg.cn/qqfile/QQNT/9.9.31/release/092069d7/QQ_9.9.31_260528_x64_01.exe",
  "filename": "QQ_9.9.31.260528_x64.exe",
  "size": 314572800,
  "hashes": {
    "sha256": "0beb5cb4ff776cba822caa0abadbd53f5892f12628a86dabea1b372c82c086bc",
    "sha1": "a1b2c3d4e5f6...",
    "md5": "d41d8cd98f00..."
  },
  "last_modified": "Thu, 28 May 2026 02:17:54 GMT"
}
```

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
  └─ 下载安装包 → 计算多格式哈希 → 生成校验文件和元数据 → 创建 GitHub Release
```

## 使用方式

### 通过 GitHub API 获取最新版本信息

```bash
# 获取最新 Release 完整信息
curl -s https://api.github.com/repos/ExLei/QQ.NT-windows-Updater/releases/latest

# 仅获取版本号
curl -s https://api.github.com/repos/ExLei/QQ.NT-windows-Updater/releases/latest | jq -r '.tag_name | ltrimstr("v")'

# 下载 metadata.json 并提取 SHA256
curl -sLO $(curl -s https://api.github.com/repos/ExLei/QQ.NT-windows-Updater/releases/latest | jq -r '.assets[] | select(.name=="metadata.json") | .browser_download_url')
cat metadata.json | jq -r '.hashes.sha256'
```

### 校验安装包完整性

```bash
# 下载校验文件和安装包
curl -sLO https://github.com/ExLei/QQ.NT-windows-Updater/releases/latest/download/QQ_9.9.31.260528_x64.exe.sha256
curl -sLO https://github.com/ExLei/QQ.NT-windows-Updater/releases/latest/download/QQ_9.9.31.260528_x64.exe

# 校验
sha256sum -c QQ_9.9.31.260528_x64.exe.sha256
```

### 包管理器集成

#### Scoop

```powershell
scoop bucket add exlei https://github.com/Exlei/Scoop-Bucket
scoop install exlei/Tencent.QQ.NT
```

#### Winget

可基于 `metadata.json` 中的 `hashes.sha256` 创建 Winget 清单，参考 [Winget 包规范](https://learn.microsoft.com/en-us/windows/package-manager/package/)。

#### Chocolatey

可基于 `metadata.json` 中的 `hashes.sha256` 创建 nuspec 包，参考 [Chocolatey 包规范](https://docs.chocolatey.org/en-us/create/create-packages)。

## 免责声明

本项目仅用于版本追踪和校验，安装包版权归腾讯所有。所有安装包均从腾讯官方 CDN 下载，未做任何修改。

本项目基于 [MIT License](./LICENSE) 发布。安装包不属于本项目，归其各自所有者所有。本项目仅作存档用途，不对任何文件进行修改。

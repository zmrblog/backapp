# backapp
windows下备份软件数据的工具，使用deepseek v4 pro为基础写的代码，用gemini pro和glm 5v turbo优化完善，代码可能还有不完善的地方，需要的就自己继续修改完善。

下载地址https://github.com/zmrblog/backapp/releases/tag/backsoftdata

# Windows 软件数据备份工具 (AppBackupTool)
一键备份 Windows 系统上已安装软件的配置数据、注册表项和程序文件，支持 AES-256-GCM 加密打包为 `.zmr` 文件，并提供完整的还原能力。

## ✨ 功能特性

| 功能 | 说明 |
|------|------|
| 📦 **智能扫描** | 自动发现注册表 / Program Files / winget / 开始菜单中的所有已安装软件 |
| 🔍 **自动搜索** | 无需配置规则即可通过关键词评分匹配定位配置文件、注册表、安装目录 |
| 📋 **精准规则** | 内置 32 条默认规则，支持用户自定义添加/修改/删除 |
| 🔐 **加密备份** | AES-256-GCM 加密 + PBKDF2 密钥派生，输出 `.zmr` 格式 |
| ♻️ **完整还原** | 从 `.zmr` 包中还原配置、注册表、程序文件，支持预览确认 |
| 🪟 **绿色软件支持** | 可手动指定非标准安装目录的软件路径 |
| 📝 **详细日志** | 自动生成 `.log` 文件到备份输出目录，方便排查问题 |

## 🚀 快速开始

### 从源码运行

```bash
# 安装依赖
pip install PyQt6 cryptography psutil pywin32

# 启动
python backappv7.0.py
```

### 使用预编译 EXE

下载 `dist/AppBackupTool.exe`，**以管理员身份运行**。

### 构建

```bash
pyinstaller --onefile --windowed --name AppBackupTool backappv7.0.py
```

## 📖 使用指南

### 备份

1. 点击 **「扫描软件」** — 等待扫描完成，列表展示所有已安装软件
2. **勾选** 需要备份的应用（搜索框可过滤）
3. 在「备份设置」中选择：
   - **输出目录** — 备份包存放位置
   - **加密备份包** — 可选，设置密码进行 AES-256-GCM 加密
   - **备份内容** — 配置文件 / 注册表 / 安装程序（勾选所需项）
   - **自定义目录** — 绿色软件手动指定安装路径（仅单选时生效）
4. 点击 **「开始备份」**

### 还原

1. 切换到 **「♻️ 还原」** 标签页
2. 选择 `.zmr` 备份文件
3. 输入密码（如备份时加密了）
4. 点击 **「查看信息」** 查看备份概要，或 **「预览内容」** 查看文件清单
5. 确认无误后点击 **「开始还原」**

### 设置

- 在 **「⚙️ 设置」** 标签页中管理备份规则
- 支持添加自定义路径规则，以便精准备份特定软件

## 🔧 技术架构

```
backappv7.0.py                  # 单文件主体
├── 加密模块 (AES-256-GCM)       # cryptography
├── 扫描引擎 (AppScanner)        # 注册表/文件系统/winget/开始菜单
├── 备份引擎 (BackupManager)     # 自动搜索 + 规则回退
├── 还原引擎 (RestoreManager)    # 事务式还原 (rename-then-copy)
├── GUI (PyQt6)                  # 备份/还原/设置三标签页
└── 格式模块 (ZMR)               # ZIP + AES 加密容器
```

### 自动搜索策略

当软件不在默认规则中时，系统通过三级自动搜索定位数据：

| 维度 | 搜索范围 | 方法 |
|------|---------|------|
| 配置文件 | AppData / ProgramData / Documents | 关键词评分 + 目录枚举 |
| 注册表 | HKCU / HKLM (含 Wow6432Node) | 子键名匹配 |
| 安装目录 | Program Files / C:\ / D:\ | 路径解析 + 进程检测 |

## ⚙️ 内置默认规则 (32 条)

微信、QQ、TIM、迅雷、Steam、Notepad++、VS Code、WPS Office、Foxmail、Xshell、
Chrome、Firefox、Edge、PuTTY、Git、7-Zip、Everything、PotPlayer、IDM、
Microsoft Office、WinRAR、QQ 影音、网易云音乐、百度网盘、向日葵、ToDesk、
钉钉、飞书、OBS Studio、Blender、**360 压缩**、**搜狗输入法**

## 📋 备份文件格式

`.zmr` = ZIP + AES-256-GCM 加密（可选）

```
backup_20260502_114550.zmr
├── manifest.json          # 元数据（版本/时间/应用列表/哈希）
├── wechat/                # 微信
│   ├── files/             # 配置文件备份
│   ├── registry/          # 注册表 .reg 导出
│   └── program/           # 安装目录文件
└── ...
```

## 🛠 依赖

| 包 | 用途 |
|----|------|
| `PyQt6` | GUI 框架 |
| `cryptography` | AES-256-GCM 加密 |
| `psutil` | 进程检测（可选） |
| `pywin32` | Windows 注册表操作 |

## 📄 License

MIT

---

> 日志文件位于备份输出目录下，遇到问题请附上日志内容提交 Issue。

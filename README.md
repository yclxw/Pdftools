# PDF发票打印工具 for Win7

<div align="center">

![Version](https://img.shields.io/badge/version-v1.69.20260604-blue)
![Platform](https://img.shields.io/badge/platform-Windows%207%2B-lightgrey)
![Electron](https://img.shields.io/badge/electron-22.3.27-9feaf9)
![License](https://img.shields.io/badge/license-MIT-green)

**一款面向财务/行政人员的 Windows 桌面 PDF 发票批量打印工具**

支持 PDF 查看、发票二合一拼版、静默打印、文档拆分合并，专为 Windows 7 兼容性优化

</div>

---

## ✨ 功能特性

- **📄 PDF 阅读器** — 内置 PDF 查看器，支持连续滚动/单页模式、缩放（10%~500%）、适应宽度/页面
- **🖨️ 发票批量打印** — 支持多张发票 PDF 拖拽导入，一键批量打印
- **📐 二合一拼版** — 自动将 2 张发票排版到 1 张 A4 纸上，节约 50% 纸张
- **🔇 静默打印** — 通过 SumatraPDF 引擎后台发送到打印机，无需用户交互
- **👁️ 打印预览** — 左侧设置右侧预览，实时确认拼版和排版效果
- **✂️ 文档拆分** — 支持每页独立、每 N 页一组、自定义范围三种拆分模式
- **🔗 文档合并** — 多 PDF 拖拽排序合并，支持上移下移调整顺序
- **🗂️ 多标签页** — 同时打开多个 PDF 文件，标签支持拖拽排序、右键关闭
- **🖥️ Win7 兼容** — 基于 Electron 22（最后支持 Windows 7 的版本），经完整测试
- **🌏 中文优化** — 完整 CJK 字体映射支持，中文打印机名不乱码

---

## 📸 界面预览

```
┌──────────────────────────────────────────────┐
│  阅读  打印  拆分  合并  视图  帮助             │  ← 菜单栏
├──────────────────────────────────────────────┤
│  [标签1] [标签2] ... [+]                      │  ← 标签栏
├──────────────────────────────────────────────┤
│                                              │
│          PDF发票打印工具 for Win7              │
│      PDF阅读 · 发票批量打印 · 文件拆分合并     │  ← 欢迎页
│    [📂 阅读] [🖨️ 打印] [✂️ 拆分] [🔗 合并]   │
│                                              │
├──────────────────────────────────────────────┤
│  就绪                                        │  ← 状态栏
└──────────────────────────────────────────────┘
```

---

## 🔧 系统需求

| 项目 | 最低配置 | 推荐配置 |
|------|----------|----------|
| 操作系统 | Windows 7 SP1 x64 | Windows 10/11 x64 |
| 处理器 | 1 GHz | 2 GHz+ |
| 内存 | 2 GB RAM | 4 GB RAM+ |
| 硬盘 | 200 MB | 500 MB |
| 打印机 | Windows 兼容打印机 | — |
| 显示 | ≥ 1024 × 768 | ≥ 1366 × 768 |

---

## 📥 安装

### 方式一：安装程序（推荐）

从 [Releases]() 页面下载最新版本 `PDF发票打印工具 for Win7_vXX.YYYYMMDD.exe`，双击运行安装向导。

默认安装路径：`C:\Program Files\PDF发票打印工具 for Win7`

### 方式二：便携版

下载便携版压缩包，解压到任意目录，运行 `PDF发票打印工具 for Win7.exe` 即可，无需安装。

---

## 🚀 快速开始

### 发票打印（主要工作流）

1. **启动应用** — 双击桌面图标或开始菜单快捷方式
2. **打开打印工作区** — 点击欢迎页 [🖨️ 打印] 或菜单栏 [打印]
3. **添加发票** — 将 PDF 发票文件拖拽到虚线框中（支持多选）
4. **选择打印机** — 在下拉列表中选择目标打印机
5. **开始打印** — 点击 [开始打印]，系统自动拼版并发送到打印机

> 💡 **提示**：每 2 张发票自动拼到 1 张 A4 纸上。如需设置双面打印或纸张类型，点击 [打印属性] 打开 Windows 打印机属性对话框。右侧预览区可实时确认排版效果。

### PDF 查看

- **打开文件**：点击 [📂 阅读]，或直接拖拽 PDF 到窗口
- **多标签**：支持同时打开多个 PDF，标签可拖拽排序
- **缩放**：`Ctrl+=` 放大 / `Ctrl+-` 缩小 / `Ctrl+鼠标滚轮`
- **适应**：`Ctrl+1` 适应宽度 / `Ctrl+2` 适应页面 / `Ctrl+3` 实际大小
- **翻页**：`PageUp/PageDown` 或点击翻页按钮

### 文档拆分

1. 菜单栏 [拆分] 或欢迎页 [✂️ 拆分]
2. 选择源 PDF 文件
3. 选择拆分方式（每页独立 / 每 N 页一组 / 自定义范围如 `1-3,5,7-9`）
4. 选择输出目录 → 点击 [开始拆分]

### 文档合并

1. 菜单栏 [合并] 或欢迎页 [🔗 合并]
2. 拖拽或选择多个 PDF 文件
3. 调整合并顺序（↑ 上移 / ↓ 下移）
4. 选择输出路径 → 点击 [开始合并]

---

## 🛠️ 开发

### 环境准备

```bash
# 克隆仓库
git clone <repo-url>
cd "PDF发票打印工具 for Win7"

# 安装依赖
npm install
```

### 常用命令

| 命令 | 说明 |
|------|------|
| `npm start` | 启动开发模式 |
| `npm run build` | 构建 NSIS 安装程序（版本号自动递增） |
| `npm run build:portable` | 构建便携版 |
| `build-portable.bat` | Windows 一键构建便携版（双击运行） |

### 技术栈

| 技术 | 版本 | 用途 |
|------|------|------|
| [Electron](https://www.electronjs.org/) | 22.3.27 | 桌面应用框架（最后支持 Win7） |
| [PDF.js](https://github.com/mozilla/pdf.js) | 3.11.174 | PDF 渲染引擎 |
| [pdf-lib](https://github.com/Hopding/pdf-lib) | 1.17.1 | PDF 创建、拼版、拆分、合并 |
| [SumatraPDF](https://www.sumatrapdfreader.org/) | 便携版 | 静默打印引擎 |
| [electron-builder](https://www.electron.build/) | 24.9.1 | 应用打包分发 |

---

## 📁 项目结构

```
PDF发票打印工具 for Win7/
├── main.js                  # Electron 主进程（980 行）
├── preload.js               # IPC 桥接（43 行）
├── start.js                 # 开发启动器脚本（12 行）
├── build.js                 # 构建脚本（34 行）
├── build-portable.bat       # 便携版构建批处理（3 行）
├── build-portable.ps1       # 便携版构建 PowerShell 脚本（52 行）
├── installer.nsh            # NSIS 安装器配置（1 行）
├── package.json             # 项目配置与依赖
├── src/
│   ├── index.html           # 主界面 HTML + CSP（212 行）
│   ├── styles.css           # 全局样式表（854 行）
│   ├── app.js               # 渲染进程入口（244 行）
│   ├── tabs.js              # 标签页管理器（306 行）
│   ├── pdf-viewer.js        # PDF 渲染器（174 行）
│   ├── print-panel.js       # 打印工作区面板（503 行）
│   ├── split-panel.js       # PDF 拆分面板（195 行）
│   └── merge-panel.js       # PDF 合并面板（225 行）
├── assets/
│   ├── pdftools.png         # 应用图标 (PNG)
│   ├── pdftools.ico         # 应用图标 (ICO)
│   ├── 蓝编辑.ico            # 工具栏按钮图标（阅读）
│   ├── 蓝星星.ico            # 工具栏按钮图标（合并）
│   ├── 蓝打印.ico            # 工具栏按钮图标（打印）
│   └── 蓝属性2.ico           # 工具栏按钮图标（拆分）
├── SumatraPDF/
│   ├── SumatraPDF.exe       # SumatraPDF 便携版
│   └── SumatraPDF-settings.txt
├── bak/                     # 构建输出目录
├── docs/                    # 项目文档（7 份）
└── node_modules/            # 依赖包

总代码量：~3,693 行
```

---

## 📖 文档

| 文档 | 说明 |
|------|------|
| [01 系统说明](docs/01%20系统说明.md) | 系统架构、数据流、安全模型、开发环境（含大模型信息） |
| [02 版本说明](docs/02%20版本说明.md) | 版本管理策略、递增规则、兼容性矩阵、发布历史 |
| [03 业务说明](docs/03%20业务说明.md) | 产品定位、业务流程、用户视角操作指南 |
| [04 技术说明](docs/04%20技术说明.md) | 技术栈、进程模型、IPC 通道、子系统详解、文件清单 |
| [05 UI说明](docs/05%20UI说明.md) | 设计系统、布局结构、交互状态、组件说明 |
| [06 备份说明](docs/06%20备份说明.md) | 备份策略、文件清单、恢复步骤、bak 目录说明 |
| [07 开发日志](docs/07%20开发日志.md) | 版本变更记录（Changelog）、代码审查记录 |

文档共 7 份，覆盖系统、版本、业务、技术、UI、备份、开发日志七个维度。

---

## ❓ 常见问题

### Q: 为什么选择 Electron 22 而不是最新版？

Electron 22 是最后一个官方支持 Windows 7/8/8.1 的版本。大量企事业单位仍在使用 Windows 7，本工具的目标用户群体需要 Win7 兼容性。

### Q: 打印时提示"未找到 SumatraPDF.exe"怎么办？

SumatraPDF 随应用打包分发。如果缺失，请重新安装应用，或手动下载 [SumatraPDF 便携版](https://www.sumatrapdfreader.org/download-free-pdf-viewer) 放置到安装目录下的 `resources/SumatraPDF/` 文件夹。

### Q: 发票排版后文字太小/太大怎么办？

调整打印工作区左侧的页边距滑块（0~3cm）。边距越小，发票可用的页面空间越大。如果发票原始尺寸特殊（非标准 A5/A4），缩放结果可能不理想。

### Q: 打印机列表为空？

点击打印机选择器旁的 [🔄] 刷新按钮。如果仍为空，请检查 Windows 控制面板中是否已安装打印机驱动。

### Q: 拆分文档的自定义范围怎么写？

使用逗号分隔范围，每个范围可以是单页（如 `5`）或起止页（如 `1-3`）。例如：`1-3,5,7-9` 表示第 1~3 页、第 5 页、第 7~9 页分别拆分为独立文件。

---

## 🤝 贡献

本项目为内部工具，暂未开放外部贡献。如有建议或问题，请联系作者。

### 贡献者

- **帅气的锅巴** — 架构设计、核心开发、文档编写

---

## 📄 许可

本项目代码采用 MIT 许可证。

使用以下开源组件，各组件遵循其原始许可：

- [Electron](https://github.com/electron/electron) — MIT
- [PDF.js](https://github.com/mozilla/pdf.js) — Apache-2.0
- [pdf-lib](https://github.com/Hopding/pdf-lib) — MIT
- [SumatraPDF](https://github.com/sumatrapdfreader/sumatrapdf) — GPL-3.0
- [electron-builder](https://github.com/electron-userland/electron-builder) — MIT

### 开发辅助

本项目开发过程中使用了以下 AI 大模型辅助：

- **Claude Code (Anthropic)** — 基于 Claude Opus 4.8 / Sonnet 4.6 系列模型，用于代码生成、架构设计、代码审查
- **DeepSeek-V4 Pro** — 用于代码分析、文档生成、逻辑推理

---

<div align="center">

**PDF发票打印工具 for Win7** — 让发票打印更高效

</div>

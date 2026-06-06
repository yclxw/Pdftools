# 会计必备PDF工具

> PDF 阅读 · 发票批量拼版打印 · 内容遮挡脱敏 · 文件拆分合并

![Version](https://img.shields.io/badge/version-v2.01-blue)
![Electron](https://img.shields.io/badge/electron-22.3.27-brightgreen)
![Platform](https://img.shields.io/badge/platform-Windows%207%2B-lightgrey)
![License](https://img.shields.io/badge/license-MIT-green)

---

## 简介

**会计必备PDF工具** 是一款面向财务/行政人员的 Windows 桌面 PDF 工具，专为发票批量处理设计。

### 核心功能

- **📖 PDF 阅读**：多标签页 PDF 查看器，支持连续滚动/单页模式、缩放、适应宽度/页面
- **🧾 发票拼版打印**：二合一 A4 拼版引擎，两张发票自动排版到一张 A4 纸，节约 50% 纸张
- **📄 文件直通打印**：单文件/批量打印，支持页面方向、纸张方向、页码范围过滤
- **🔒 内容遮挡**：PDF 页面敏感信息脱敏，矩形遮罩 + 自定义文字覆盖，支持中文字体
- **✂️ PDF 拆分**：每页独立 / 每 N 页一组 / 自定义范围三种拆分模式
- **🔗 PDF 合并**：多文件拖拽排序合并，支持上移下移调整顺序
- **📑 页面管理**：PDF 页面旋转、删除、重排序

### 特色

- ✅ **Windows 7 兼容**：基于 Electron 22（最后支持 Win7 的版本），支持 Win7 SP1 x64+
- ✅ **离线可用**：所有功能本地完成，无需网络连接
- ✅ **绿色便携**：提供免安装便携版，解压即用
- ✅ **中文友好**：完整 CJK 字体支持，中文界面、中文发票无乱码
- ✅ **轻量级**：不依赖 Adobe Acrobat，内置所有 PDF 处理引擎

---

## 快速开始

### 下载

从 [Releases](../../releases) 页面下载最新便携版 EXE，直接运行即可。

### 从源码运行

```bash
# 1. 克隆仓库
git clone <repo-url>
cd pdftools

# 2. 安装依赖
npm install

# 3. 开发运行
npm start

# 4. 构建便携版
npm run build:portable
```

---

## 菜单结构

```
阅读    发票    打印    工具 ▼     视图 ▼            帮助
                       ──────    ───────────       ──────
                      拆分文档    默认视图定义        设为默认软件
                      合并文档    ───────────        ───────────
                      页面管理    适应宽度 Ctrl+1    关于
                                 适应页面 Ctrl+2
                                 实际大小 Ctrl+3
                                 ───────────
                                 放大     Ctrl+=
                                 缩小     Ctrl+-
                                 ───────────
                                 切换开发者工具
```

---

## 技术栈

| 层级 | 技术 | 版本 |
|------|------|------|
| 桌面框架 | Electron | 22.3.27 |
| PDF 渲染 | pdfjs-dist | 3.11.174 |
| PDF 操作 | pdf-lib + @pdf-lib/fontkit | 1.17.1 |
| 静默打印 | SumatraPDF | 便携版 |
| 打包分发 | electron-builder | 24.9.1 |

---

## 项目结构

```
├── main.js              # Electron 主进程（1,319 行）
├── preload.js           # 安全桥接（49 行）
├── start.js             # 开发启动器（9 行）
├── build.js             # 构建脚本（26 行）
├── package.json         # 项目配置
├── src/
│   ├── index.html       # 主界面（214 行）
│   ├── styles.css       # 全局样式（1,047 行）
│   ├── app.js           # 渲染入口 + getCMapUrl()（391 行）
│   ├── tabs.js          # 标签管理（352 行）
│   ├── pdf-viewer.js    # PDF 渲染器（176 行）
│   ├── print-panel.js   # 打印工作区（638 行）
│   ├── split-panel.js   # 拆分面板（195 行）
│   ├── merge-panel.js   # 合并面板（227 行）
│   └── page-manager.js  # 页面管理（392 行）
├── docs/                # 项目文档（7 份）
├── assets/              # 应用图标（含 .ico 按钮图标）
├── SumatraPDF/          # SumatraPDF 便携版
└── bak/                 # 构建输出 + 源码备份
```

**总计：5,035 行源代码**

---

## 文档

| 文档 | 说明 |
|------|------|
| [01 系统说明](docs/01%20系统说明.md) | 系统架构、数据流、安全模型、开发环境、大模型信息 |
| [02 版本说明](docs/02%20版本说明.md) | 版本管理策略、发布历史、兼容性矩阵 |
| [03 业务说明](docs/03%20业务说明.md) | 产品定位、业务流程、用户场景故事 |
| [04 技术说明](docs/04%20技术说明.md) | 技术栈、核心子系统、IPC 通道、构建配置 |
| [05 UI 说明](docs/05%20UI说明.md) | 设计系统、色彩体系、布局结构、交互状态 |
| [06 备份说明](docs/06%20备份说明.md) | 备份策略、文件清单、恢复步骤 |
| [07 开发日志](docs/07%20开发日志.md) | 版本变更记录、代码审查记录 |

---

## 系统需求

| 项目 | 最低配置 | 推荐配置 |
|------|----------|----------|
| 操作系统 | Windows 7 SP1 x64 | Windows 10/11 x64 |
| 内存 | 2 GB | 4 GB+ |
| 硬盘 | 200 MB | 500 MB |
| 分辨率 | 1024×768 | 1366×768+ |

---

## 开发

### 开发工具

- **IDE**：Visual Studio Code
- **运行时**：Node.js 16.17.1
- **AI 辅助**：Claude Opus 4.8 / Sonnet 4.6 (Anthropic)、DeepSeek-V4 Pro

### 构建命令

```bash
npm start              # 开发运行
npm run build          # 构建 NSIS 安装程序
npm run build:portable # 构建便携版 (x64)
```

---

## 许可

本项目代码采用 MIT 许可证。使用以下开源组件：

- [Electron](https://www.electronjs.org/) — MIT
- [PDF.js](https://github.com/mozilla/pdf.js) — Apache-2.0
- [pdf-lib](https://github.com/Hopding/pdf-lib) — MIT
- [SumatraPDF](https://www.sumatrapdfreader.org/) — GPLv3

各组件遵循其原始许可协议。

---

## 作者

**帅气的锅巴** — 架构设计、核心开发、文档编写

---

*最后更新：2026-06-06 · v2.01*

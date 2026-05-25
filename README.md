# PDF发票打印工具 for Win7

```
版本:       v1.46.20260525
平台:       Windows 7 SP1 x64+
Electron:   22.3.27  (Chromium 108, Node.js 16.17.1)
许可:       MIT
维护者:     帅气的锅巴
```

## 1. 概述

PDF发票打印工具是一款 Windows 桌面应用，提供多标签 PDF 阅读和发票批量打印功能。工具面向 Windows 7 SP1 x64，并在 Windows 10/11 上验证通过。

应用的核心是 **2合1 矢量拼版引擎**：将发票页面按 1cm 边距排列在 A4 纸张上，通过 SumatraPDF 命令行接口静默发送到打印机。

### 1.1 设计约束

- 目标平台 Windows 7 SP1 (NT 6.1)，不使用 Windows 8+ 专有 API
- Electron 版本 ≤ 22（最后支持 Win7 的版本系列）
- Chromium ≥ 92（pdfjs-dist 3.x 依赖 ES2022 的 `.at()` 方法）
- 所有 PDF 渲染在渲染进程完成 (Chromium Canvas)，不经过 Node.js 主进程
- 构建产物为自包含 NSIS 安装包，用户无需安装额外运行时

### 1.2 功能速览

| 功能 | 快捷键/操作 | 说明 |
|------|-------------|------|
| 多标签阅读 | — | 浏览器式标签管理，支持拖拽排序和右键菜单 |
| 适应宽度 | `Ctrl+1` | 页面宽度填满视口 |
| 适应页面 | `Ctrl+2` | 页面高度填满视口 |
| 实际大小 | `Ctrl+3` | 100% 原始尺寸 |
| 无极缩放 | `Ctrl+滚轮` | 10% 步进自由缩放 |
| 翻页 | `PageUp/Down` | 上一页/下一页 |
| 首末页 | `Home/End` | 跳到首页/末页 |
| 发票打印 | — | 拖拽添加队列，2合1 拼版，静默出纸 |
| 打印机管理 | — | 自动枚举，手动刷新，属性设置 |
| 默认关联 | — | 帮助菜单调用系统对话框 |

### 1.3 菜单结构

```
打开PDF    发票打印      视图 ▼         帮助 ▼
(打开文件)  (打印面板)   适应宽度  Ctrl+1  设为默认软件
                        适应页面  Ctrl+2  ─────────
                        实际大小  Ctrl+3  关于
                        放大      Ctrl+=
                        缩小      Ctrl+-
                        切换开发者工具
```

## 2. 环境要求

### 2.1 运行环境

| 组件 | 要求 |
|------|------|
| 操作系统 | Windows 7 SP1 x64 及以上 |
| 系统更新 | KB2533623 (Windows 7 必需) |
| 运行时 | Visual C++ 2015 Redistributable (x64) |
| 打印机 | 已安装打印机驱动 (打印功能需要) |

### 2.2 开发环境

| 组件 | 版本 | 用途 |
|------|------|------|
| Node.js | 18.20.x LTS | JavaScript 运行时 |
| npm | 9.x | 包管理器 |
| Git | 2.x | 版本控制 |
| VS Code | 1.98+ | 代码编辑器 |
| PowerShell | 5.1 | 构建脚本与系统操作 |
| 7-Zip | 21.07 | 压缩包解压 (electron-builder 依赖) |

## 3. 架构

### 3.1 进程拓扑

```
MAIN (main.js)
  ├── IPC 处理器 (13 通道)
  ├── 拼版引擎 (runImposition)
  ├── SumatraPDF 打印调度 (execFile)
  ├── 窗口生命周期管理
  ├── 自定义菜单构建
  └── 单实例锁 + 命令行文件打开

RENDERER (src/)
  ├── TabManager   — 标签状态机、拖拽排序、右键上下文菜单
  ├── PdfViewer    — pdfjs-dist Worker → Canvas、缩放/滚动/页码追踪
  └── PrintPanel   — 文件拖放队列、打印机管理、进度条反馈

PRELOAD (preload.js)
  └── contextBridge.exposeInMainWorld('electronAPI', { ... })
      暴露 10 个安全 API 到渲染进程
```

### 3.2 打印流水线数据流

```
print-panel.js:_startPrint()
  → ipcRenderer.invoke('print:executePrint', filePaths, printerName)
  → main.js:ipcMain.handle('print:executePrint', ...)
    → runImposition(filePaths)              // pdf-lib 2合1 排版
    → fs.writeFileSync(tempPdf)             // 写入临时文件
    → execFile(sumatraPath, ['-print-to', ...])
    → fs.unlinkSync(tempPdf)                // 清理临时文件
  → sendToRenderer('print:progress', ...)    // 实时进度反馈
```

### 3.3 SumatraPDF 路径解析

```
getSumatraPath():
  候选 1: {exe目录}/SumatraPDF.exe
  候选 2: {exe目录}/SumatraPDF/SumatraPDF.exe
  候选 3: {resources}/SumatraPDF.exe
  候选 4: {resources}/SumatraPDF/SumatraPDF.exe

返回值: 首个 fs.existsSync() 为 true 的路径
日志: 所有候选路径检查结果写入 app.log
```

### 3.4 IPC 通道一览

| 通道 | 方向 | 类型 | 功能 |
|------|------|------|------|
| `dialog:openPdf` | 渲染→主 | handle | 打开 PDF 文件选择对话框 |
| `dialog:selectInvoices` | 渲染→主 | handle | 打开发票文件选择对话框 |
| `fs:readFile` | 渲染→主 | handle | 读取文件二进制内容 |
| `fs:getFileInfo` | 渲染→主 | handle | 获取文件名和大小 |
| `print:getPrinters` | 渲染→主 | handle | 枚举 Windows 打印机列表 |
| `print:executePrint` | 渲染→主 | handle | 执行打印流水线 |
| `print:printerProperties` | 渲染→主 | handle | 打开打印机属性对话框 |
| `print:progress` | 主→渲染 | send | 打印阶段进度通知 |
| `menu:openPdf` | 主→渲染 | send | 菜单 → 打开PDF |
| `menu:printPanel` | 主→渲染 | send | 菜单 → 发票打印 |
| `open-file` | 主→渲染 | send | 命令行或双击打开 PDF |
| `view:fitWidth` 等 | 主→渲染 | send | 视图缩放命令 (6 通道) |

## 4. 拼版引擎

### 4.1 常量

```
A4_WIDTH     = 595.28   // 磅 (pt)
A4_HEIGHT    = 841.89   // 磅
MARGIN       = 28.35    // 1cm = 28.35pt
availWidth   = 538.58   // A4_WIDTH  - 2 * MARGIN
availHeight  = 785.19   // A4_HEIGHT - 2 * MARGIN
halfHeight   = 392.595  // availHeight / 2
```

### 4.2 主算法

```
runImposition(filePaths):
  outputDoc = PDFDocument.create()
  font = outputDoc.embedFont(StandardFonts.Helvetica)

  for i = 0; i < len(filePaths); i += 2:
    page = outputDoc.addPage([A4_WIDTH, A4_HEIGHT])

    // 上半部分：发票 i，底部对齐到中线
    _embedInvoice(outputDoc, page, filePaths[i], font,
      x=MARGIN, y=MARGIN+halfHeight,
      maxWidth=availWidth, maxHeight=halfHeight,
      alignTo='top')

    // 下半部分：发票 i+1（如存在），顶部对齐到中线
    if i+1 < len(filePaths):
      _embedInvoice(outputDoc, page, filePaths[i+1], font,
        x=MARGIN, y=MARGIN,
        maxWidth=availWidth, maxHeight=halfHeight,
        alignTo='bottom')

  return outputDoc.save()
```

### 4.3 缩放与对齐

```
_embedInvoice(...):
  srcDoc = PDFDocument.load(sourcePath)
  [embeddedPage] = outputDoc.embedPdf(srcDoc, [0])
  dims = embeddedPage.size()

  fitScale  = maxWidth / dims.width     // 仅按宽度适配
  drawW     = dims.width  * fitScale    // 等比缩放宽度
  drawH     = dims.height * fitScale    // 等比缩放高度

  drawX = x + (maxWidth - drawW) / 2    // 水平居中

  if alignTo == 'top':
    drawY = y + maxHeight - drawH        // 底部贴中线
  elif alignTo == 'bottom':
    drawY = y                            // 顶部贴中线

  targetPage.drawPage(embeddedPage, {
    x: drawX, y: drawY,
    width: drawW, height: drawH
  })
```

> 高度不设约束。若 `drawH > maxHeight` 图像将溢出其半区。此为设计意图：发票通常宽度大于高度，按宽度填充可获得最大可读尺寸。

### 4.4 排版效果

```
单张发票                    多张发票 (5张为例)

┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│     发票1         │  │     发票1         │  │     发票5         │
│   (适应宽度)      │  │   (上半部分)      │  │   (上半部分)      │
├─ ─ ─ ─ ─ ─ ─ ─ ┤  ├──────────────────┤  │                  │
│     (空白)        │  │     发票2         │  │     (空白)       │
└──────────────────┘  └──────────────────┘  └──────────────────┘
     第1页 (1张)           第1页                  第3页

                       ┌──────────────────┐
                       │     发票3         │
                       ├──────────────────┤
                       │     发票4         │
                       └──────────────────┘
                            第2页
```

## 5. 平台兼容性

### 5.1 Windows 7 打印机枚举

```
print:getPrinters:
  osver = os.release().split('.').map(Number)
  if osver[0] < 10:                     // NT 6.x = Win7/8/8.1
    return wmic printer get name         // Get-Printer 不可用
  else:
    return Get-Printer | Select Name     // Win8+ 原生 PowerShell cmdlet
```

`Get-Printer` 需要 PrintManagement 模块 (Windows 8+/Server 2012+)。在 NT 6.x 上 `wmic` 提供等效输出，避免 10 秒 PowerShell 超时。

### 5.2 中文字体回退

```css
body {
  font-family:
    -apple-system, BlinkMacSystemFont,
    "Segoe UI", "Microsoft YaHei",    /* Windows 10/11 */
    "SimHei", "SimSun",               /* Windows 7 回退 */
    sans-serif;
}
```

Windows 7 默认未安装 Microsoft YaHei。SimHei (黑体) 和 SimSun (宋体) 自 XP 起在所有中文 Windows 中存在。

### 5.3 Windows 7 运行时依赖

| 依赖 | 文件名 | 原因 |
|------|--------|------|
| Service Pack 1 | KB976932 | Electron 22 最低系统要求 |
| KB2533623 | Windows6.1-KB2533623-x64.msu | `SetDefaultDllDirectories` API (Chromium 90+ 需要) |
| VC++ 2015 Redist | VC_redist.x64.exe | Electron 运行时 C++ 库依赖 |

## 6. 技术栈

| 组件 | 版本 | 角色 |
|------|------|------|
| Electron | 22.3.27 | 桌面框架 (窗口管理、IPC、系统集成) |
| Chromium | 108 | 渲染引擎 (HTML/CSS/JS/Canvas) |
| Node.js | 16.17.1 | 主进程 JavaScript 运行时 |
| pdfjs-dist | 3.11.174 | PDF 解析与 Canvas 渲染 |
| pdf-lib | 1.17.1 | PDF 矢量创建与页面拼版 |
| SumatraPDF | 3.2+ | 命令行静默打印驱动 |
| electron-builder | 24.9.1 | 应用打包与分发 |
| NSIS | 3.0.4.1 | Windows 安装包制作 |
| rcedit | — | PE 文件资源编辑 (图标/版本信息) |

### 6.1 版本选择依据

```
Electron 22.3.27 (Chromium 108) + pdfjs-dist 3.11.174
```

| 约束 | 条件 | 方案 |
|------|------|------|
| 支持 Windows 7 | Electron ≤ 22 | 22.3.27 — 最后一个 Win7 兼容版本 |
| ES2022 `.at()` API | Chromium ≥ 92 | 108 — 完整支持 |
| 中文 CMap 字体 | pdfjs-dist cmaps/ | 3.11.174 — 内置完整 CMap |
| 业务代码不变 | API 向前兼容 | pdfjs-dist 3.x — API 稳定 |

## 7. 开发环境

### 7.1 开发工具链

| 工具 | 版本 | 用途 |
|------|------|------|
| Node.js | 18.20.x LTS | JavaScript 运行时环境 |
| npm | 9.x | 包管理与脚本执行 |
| Git | 2.x | 版本控制 |
| VS Code | 1.98+ | 代码编辑与调试 |
| PowerShell | 5.1 | 构建脚本与 Windows 系统管理 |
| 7-Zip | 21.07 | 压缩归档 (electron-builder 内部依赖) |

### 7.2 VS Code 扩展

| 扩展标识 | 版本 | 用途 |
|----------|------|------|
| `anthropic.claude-code` | 2.1.144 | AI 编程助手 (代码生成、架构设计、问题诊断) |
| `ms-ceintl.vscode-language-pack-zh-hans` | 1.118 | 中文界面语言包 |

### 7.3 AI 大模型

| 模型 | 提供方 | 用途 |
|------|--------|------|
| Claude Opus 4.7 | Anthropic | 主力开发: 复杂代码生成、架构重构、深度问题诊断 |
| Claude Sonnet 4.6 | Anthropic | 辅助开发: 文档编写、代码审查、快速问答 |
| DeepSeek V4 Pro | DeepSeek | 兼容性: Windows API 分析、平台适配验证 |

本项目的 AI 开发工作流:

- `.claude/CLAUDE.md` 定义项目规则与约束，AI 自动加载
- `.claude/settings.json` 管理权限与工具配置
- 所有代码变更经 AI 辅助审查与测试验证

### 7.4 依赖安装

```powershell
# 国内镜像加速
$env:ELECTRON_MIRROR = "https://npmmirror.com/mirrors/electron/"
npm config set registry https://registry.npmmirror.com

# 安装
npm ci
```

## 8. 项目结构

```
pdftools/
├── main.js              # 主进程: IPC/拼版/打印/菜单
├── preload.js           # contextBridge 安全桥接
├── start.js             # 开发启动器
├── build.js             # 构建脚本
├── installer.nsh        # NSIS 安装器配置
├── package.json         # 依赖与构建元数据
├── CHANGELOG.md         # 项目根变更日志
├── README.md            # 项目根说明
├── assets/
│   ├── icon.ico         # Windows 图标 (多分辨率)
│   └── icon.png         # 源图标 (256×256)
├── SumatraPDF/
│   └── SumatraPDF.exe   # 打印驱动 (~19 MB)
├── docs/
│   ├── BACKUP.md        # 备份规范
│   ├── VERSION.md       # 版本规范
│   ├── CHANGELOG.md     # 变更日志
│   └── README.md        # 本文件
├── bak/
│   ├── source/          # 源代码归档
│   ├── dev-tools/       # 开发工具缓存
│   ├── patches/         # 运行时补丁
│   └── publish/         # 发布产物
└── src/
    ├── index.html       # 主页面 (含 CSP 策略)
    ├── styles.css       # 样式表 (CSS 自定义属性)
    ├── app.js           # 渲染器入口
    ├── tabs.js          # 标签管理器
    ├── pdf-viewer.js    # PDF 查看器
    └── print-panel.js   # 打印面板
```

## 9. 构建

### 9.1 前置条件

- Node.js >= 18
- npm >= 9
- Windows 10/11 (构建主机)
- Electron 镜像可访问

### 9.2 三步构建

```powershell
$env:ELECTRON_MIRROR = "https://npmmirror.com/mirrors/electron/"

# 步骤 1: 打包应用
npx electron-builder --dir --config.win.signAndEditExecutable=false
# 输出: bak/win-unpacked/

# 步骤 2: 修复 exe 元数据 + 嵌入图标
$rcedit = "$env:LOCALAPPDATA\electron-builder\Cache\winCodeSign\091439615\rcedit-x64.exe"
$exe = "bak\win-unpacked\PDF发票打印工具.exe"
& $rcedit $exe --set-version-string FileDescription "PDF发票打印工具"
& $rcedit $exe --set-version-string ProductName "PDF发票打印工具 for Win7"
& $rcedit $exe --set-version-string OriginalFilename "PDF发票打印工具.exe"
& $rcedit $exe --set-version-string InternalName "PDF发票打印工具"
& $rcedit $exe --set-version-string CompanyName "帅气的锅巴"
& $rcedit $exe --set-icon assets\icon.ico

# 步骤 3a: 生成 NSIS 安装包
npx electron-builder --win nsis --config.win.signAndEditExecutable=false `
    --prepackaged bak\win-unpacked
# 输出: bak/publish/PDF发票打印工具_v1.46.20260525.exe

# 步骤 3b: 生成便携版 exe（Win7 双击运行）
npx electron-builder --win portable --config.win.signAndEditExecutable=false `
    --prepackaged bak\win-unpacked
# 输出: bak/publish/PDF发票打印工具_v1.46.20260525_portable.exe
```

> 步骤 2 是必需的: `signAndEditExecutable=false` 跳过了 electron-builder 内置的 rcedit 调用。未执行此步骤时，exe 将保留上游 Electron 的 VERSIONINFO 字段 ("electron"、"electron.exe") 和默认 Electron 图标。

## 10. 已知问题与缓解

### 10.1 Electron 版本漂移

**症状**: `npm install` 后 Electron 版本变为 27.x，Win7 上启动无窗口。

**根因**: 依赖声明中的 `^` 前缀允许 npm 解析到更高大版本。

**缓解**: `package.json` 中 electron 使用精确版本 `"electron": "22.3.27"`。

### 10.2 UserChoice 哈希

**症状**: 直接写 `HKCU\Software\Classes\.pdf` 注册表无法更改默认 PDF 打开程序。

**根因**: Windows 7 (KB2922717+) 对 `UserChoice\Progid` 值进行哈希校验: `SHA256(ProgID + 用户 SID + 系统盐值)`。任意写入被静默忽略。

**缓解**: 使用 `shell32.dll,OpenAs_RunDLL` 调起系统"打开方式"对话框，由 Windows 内部完成哈希计算。

### 10.3 winCodeSign 符号链接

**症状**: 7za 在 Windows 上提取 winCodeSign-2.6.0.7z 时报错退出。

**根因**: 压缩包内含 macOS `.dylib` 符号链接，Windows 创建符号链接需要额外权限。

**缓解**: 尽管 7za 返回非零退出码，Windows 所需的 `rcedit-x64.exe` 和 `rcedit-ia32.exe` 已成功提取。使用缓存中的版本即可。

## 11. Windows 7 部署

| 步骤 | 文件 | 大小 | 说明 |
|------|------|------|------|
| 1 | Windows6.1-KB976932-X64.exe | 903 MB | 安装 SP1 (已装可跳过，需重启) |
| 2 | Windows6.1-KB2533623-x64.msu | 2.2 MB | 安装 KB2533623 (必须) |
| 3 | VC_redist.x64.exe | 24 MB | 安装 VC++ 2015 运行库 (必须) |
| 4 | PDF发票打印工具_v1.46.20260525.exe | 82 MB | 安装主程序 |

## 12. 文档索引

| 文档 | 路径 | 说明 |
|------|------|------|
| 备份说明 | [docs/BACKUP.md](BACKUP.md) | 备份结构定义与环境重建步骤 |
| 版本规范 | [docs/VERSION.md](VERSION.md) | 版本号格式定义与同步规则 |
| 变更日志 | [docs/CHANGELOG.md](CHANGELOG.md) | Keep a Changelog 格式的版本变更记录 |
| 项目说明 | [docs/README.md](README.md) | 本文件 |
| 项目规则 | [../.claude/CLAUDE.md](../.claude/CLAUDE.md) | Claude Code 开发规则与约束 |

## 13. 参考资料

- Electron 22.x: https://www.electronjs.org/docs/latest
- pdfjs-dist: https://github.com/mozilla/pdf.js
- pdf-lib: https://github.com/Hopding/pdf-lib
- SumatraPDF: https://www.sumatrapdfreader.org
- electron-builder: https://www.electron.build
- NSIS: https://nsis.sourceforge.io
- Keep a Changelog: https://keepachangelog.com/zh-CN/1.1.0/
- 语义化版本: https://semver.org/lang/zh-CN/

## 14. 许可

MIT © 帅气的锅巴

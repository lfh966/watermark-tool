# 图片水印工具 🔒

纯前端、零构建的图片水印 / 标注 / 长图拼接工具，内置毛玻璃主题引擎。

所有处理都在**浏览器本地**完成 —— 图片不会上传到任何服务器，网页版、PWA 与桌面版共用同一份代码。

![License](https://img.shields.io/github/license/lfh966/watermark-tool)
![Last Commit](https://img.shields.io/github/last-commit/lfh966/watermark-tool)
![Stars](https://img.shields.io/github/stars/lfh966/watermark-tool)

## ✨ 功能

**标注与编辑**
- 七种工具：矩形框、椭圆框、箭头、直线、画笔、马赛克、文字
- 撤销 / 重做（`Ctrl/⌘+Z`、`Ctrl/⌘+Shift+Z`、`Ctrl+Y`）
- 数字键 `1`–`7` 快速切换工具，`0` 退出绘制
- 画笔与文字的颜色、线宽、字号、填充、透明度可调

**水印**
- 文字水印：内容、字号、颜色、字体、粗体、描边、透明度、旋转角度、间距、边距可调
- 平铺 / 单个两种模式，平铺可降低被裁切去除的风险

**长图拼接**
- 垂直 / 水平拼接，统一尺寸或保持原尺寸
- 间距、背景色可调，可叠加水印

**主题与外观**
- 毛玻璃主题引擎：光晕强度、模糊半径、圆角、强调色实时可调
- 内置 6 套预设：极光夜色、深海蓝、午夜紫、薄荷玻璃、浅色玻璃、暖阳玻璃
- 支持导出 / 导入主题 JSON，方便分享

**导出**
- PNG / JPEG / WebP，质量可调，文件名规则可自定义
- 导出 PNG、复制到剪贴板、批量打包 ZIP
- 支持写入文件夹（Chrome / Edge）

**多端**
- 网页即开即用，可安装为 PWA 并离线使用
- 基于 Tauri 2 的桌面版，支持 Windows / macOS / Linux

## 🚀 在线使用

👉 **https://lfh966.github.io/watermark-tool/**

无需安装，打开即用。也可以把它安装成桌面应用（PWA）：Chrome / Edge 地址栏右侧点「安装」图标。

> 仓库首次部署时，需在 GitHub 仓库 **Settings → Pages → Source** 选择 **GitHub Actions**，推送 `main` 后由 `.github/workflows/pages.yml` 自动发布。

## 📦 本地运行

```bash
git clone https://github.com/lfh966/watermark-tool.git
cd watermark-tool/src
python -m http.server 8000
# 打开 http://localhost:8000
```

也可以直接双击 `src/index.html`，但 Service Worker / 安装 PWA 需要 `http://` 或 `https://`，所以推荐上面的本地服务器。

## 🖥 桌面版

需要先安装 [Rust](https://rustup.rs) 和 [Node.js](https://nodejs.org)（仅用于取 Tauri CLI）。

```bash
cd src-tauri

# 开发模式（热重载）
npx @tauri-apps/cli@2 dev

# 打包安装包
npx @tauri-apps/cli@2 build
```

如果已经装过 `tauri-cli`，把 `npx @tauri-apps/cli@2` 换成 `cargo tauri` 即可。

产物位置：

| 平台 | 路径 |
|---|---|
| Windows | `src-tauri/target/release/bundle/msi/*.msi`、`nsis/*.exe` |
| macOS | `src-tauri/target/release/bundle/dmg/*.dmg` |
| Linux | `src-tauri/target/release/bundle/deb/*.deb`、`appimage/*.AppImage` |

也可以直接从 [Releases](https://github.com/lfh966/watermark-tool/releases) 下载 CI 构建好的安装包。

> macOS 安装包未签名，首次打开需右键 → 打开 → 允许。要彻底消除提示需要 Apple 开发者账号。

## 📁 项目结构

```
watermark-tool/
├── .github/workflows/
│   ├── pages.yml              # 推送 main 时自动部署网页版
│   └── release.yml            # 推送 v* 标签时构建三平台安装包
├── src/                       # 前端（GitHub Pages 发布目录）
│   ├── index.html             # 全部 UI 与逻辑
│   ├── manifest.json          # PWA 清单
│   ├── sw.js                  # Service Worker
│   ├── vendor/jszip.min.js    # 本地内置，离线也能打 ZIP
│   └── icons/                 # 图标源文件与各尺寸 PNG / ICO
├── src-tauri/                 # Tauri 2 桌面外壳
│   ├── Cargo.toml
│   ├── build.rs
│   ├── tauri.conf.json
│   ├── capabilities/default.json
│   ├── icons/                 # 由 tauri icon 生成
│   └── src/main.rs
├── README.md
├── LICENSE
└── .gitignore
```

前端放在 `src/`，桌面版通过 `frontendDist: "../src"` 引用，避免把 `.git`、README 等无关文件打进安装包。

## 🔧 自定义

打开 `src/index.html`，搜索这些关键字：

| 关键字 | 作用 |
|---|---|
| `PRESETS` | 内置预设主题，改这里可以换默认配色 |
| `THEME_DEFAULTS` | 默认外观参数（光晕、模糊、圆角、强调色） |
| `THEME_KEY` | 主题在 localStorage 中的键名 |

修改 `index.html` 后，记得把 `src/sw.js` 顶部的 `CACHE_NAME` 版本号 `v1` → `v2`，否则已安装 PWA 的用户会继续看到旧版本。

## 🚀 发布流程

**网页版**：推送到 `main` 即触发 `pages.yml`，自动发布 `src/` 到 GitHub Pages。

**桌面版**：推一个版本标签即可触发三平台构建：

```bash
git tag -a v1.0.0 -m "首个正式版本"
git push origin v1.0.0
```

构建完成后，Releases 中会出现一个 **Draft**，检查无误后手动点 **Publish**。也可以在 Actions 页面手动运行 `Release Desktop App` 并填写标签。

发布新版本前，记得同步修改 `src-tauri/Cargo.toml` 和 `src-tauri/tauri.conf.json` 里的 `version`。

## 🎨 技术栈

- 原生 HTML / CSS / JavaScript，**零构建**（唯一第三方库 JSZip 已本地内置）
- Canvas 2D 负责所有图像处理
- JSZip 本地打包，无 CDN 依赖
- PWA：manifest + Service Worker，离线可用
- Tauri 2 作为桌面外壳，GitHub Actions 自动构建

## 🤝 贡献

欢迎提 Issue 和 PR。提交前请先跑一遍功能自测（导入图片、加水印、导出、离线打开）。

## 📄 许可证

[MIT](LICENSE) © 2026 lfh966

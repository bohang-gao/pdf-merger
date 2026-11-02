# PDF 合并工具

纯前端单页应用：选择 / 拖入多个 PDF 与 PNG/JPG 图片，合并、拆分、压缩后一键下载。**所有处理在浏览器本地完成，文件不上传任何服务器。**

## 使用

打开 `index.html` 即可（或部署后访问 Pages 地址）：

### 导入与排序
- **选择文件** / **选择整个文件夹** / 拖拽 PDF、PNG、JPG 到虚线框
- 文件夹模式自动过滤非 PDF / 图片文件，并按文件名排序
- 工具栏 **A→Z** / **Z→A** 按文件名即时排序（中文与数字感知，如 `第2章` 排在 `第10章` 前）
- 列表支持拖拽排序，也可用 ↑ ↓ 调整、✕ 移除

### 模式：合并 / 拆分提取
- **合并**（默认）：所有文件按列表顺序合成一个 PDF
  - 每个 PDF 条目可点 **⚙** 展开页码范围输入框，语法：`1-3,5`、`n`、`n-m`、`-m`、`n-`（1 起算，超出总页数部分忽略；非法输入标红并将该文件排除，不影响其他文件）
  - PNG/JPG 按 1px = 1pt 铺满一页，页面尺寸等于图片像素尺寸
- **拆分提取**：按每个 PDF 的页码范围（留空 = 全部页）各自导出，打包为 ZIP 下载（如 `发票_p1-3.pdf`、`发票_全部.pdf`）；图片文件自动跳过

### 图片压缩（可选，合并模式）
- 折叠面板中勾选「启用压缩」：图片经 canvas 重编码为 JPEG（透明区填白）
- 质量：高 0.85 / 中 0.7 / 低 0.5；最大边长：不限制 / 2480px（A4 300dpi）/ 1600px
- 只影响画质与体积，页面尺寸不变；合并后 toast 提示节省的字节数

### 输出
- footer 可自定义输出文件名（默认 `merged-<时间戳>`，非法字符自动替换为 `-`）
- 点「合并并下载」得到 `<文件名>.pdf`（拆分模式为 `.zip`）
- 点「🖨 打印」先弹出**打印预览**：左侧为打印顺序列表（↑↓ 调整，与主列表同步），右侧逐文件查看内容（PDF 用内置查看器翻页、图片直接显示），也可点「查看合并后效果」翻看整批合并结果
- 预览中点「开始打印」：临时合并（含页码范围与图片压缩设置）后调起**一次**打印对话框，整批按顺序出纸——适合发票报销整包打印（Chromium 内核浏览器支持最佳；不支持时自动改为新标签页打开手动 Ctrl+P）

加密或损坏的 PDF、无法解码的图片会被标红提示并自动跳过，不影响其余文件处理。

## PWA 离线安装

部署到 HTTPS（GitHub Pages）后，浏览器地址栏会出现「安装」图标，可将工具安装到桌面/手机主屏，**离线可用**（`sw.js` 预缓存页面与图标）。本地 `file://` 双击打开时自动跳过 SW 注册，功能不受影响。

## 部署到 GitHub Pages

1. 在 GitHub 新建仓库（如 `pdf-merger`）
2. 推送全部文件：

   ```bash
   git init && git add index.html README.md manifest.webmanifest sw.js icon-192.png icon-512.png
   git commit -m "feat: PDF merge/split/compress tool with PWA"
   git branch -M main
   git remote add origin https://github.com/<你的用户名>/pdf-merger.git
   git push -u origin main
   ```

3. 仓库 → Settings → Pages → Source 选 `Deploy from a branch`，Branch 选 `main` / `(root)`，Save
4. 约 1 分钟后访问 `https://<你的用户名>.github.io/pdf-merger/`

> 也可直接把 `index.html` 下载到本地双击打开使用（pdf-lib 与 JSZip 均已内嵌，无需联网）。

## 技术

- 合并/拆分引擎：[pdf-lib](https://github.com/Hopding/pdf-lib) 1.17.1（内嵌，无 CDN 依赖，断网可用）
- ZIP 打包：[JSZip](https://stuk.github.io/jszip/) 3.10.1（内嵌）
- 离线缓存：原生 Service Worker，cache-first 策略
- 单页应用，无框架、无后端、无构建步骤

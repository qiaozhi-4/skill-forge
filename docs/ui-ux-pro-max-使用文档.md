# UI UX Pro Max 使用文档

本文整理 [UI UX Pro Max skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) 在 Codex CLI 中的最小使用流程。它会根据产品类型和需求，提供 UI 风格、配色、字体、页面模式及技术栈相关的设计建议。

## 1. 前置要求

- 已安装 Node.js 和 npm。
- 已安装 Codex CLI。
- 已安装 Python 3.x；skill 的搜索脚本使用 Python 标准库。

检查 Python：

```bash
python3 --version
```

## 2. 安装

在终端执行：

```bash
# 全局安装 CLI
npm install -g ui-ux-pro-max-cli

# 进入需要使用 skill 的项目
cd /path/to/your/project

# 为 Codex 安装 skill
uipro init --ai codex
```

安装完成后，skill 文件通常位于项目的 `.codex/skills/` 目录中。也可以安装到全局目录，使所有项目都能使用：

```bash
uipro init --ai universal --global
```

`ui-ux-pro-max-cli` 是当前 npm 包名，命令名仍为 `uipro`；不要使用过时的 `uipro-cli` 包。

## 3. 使用

安装后直接向 Codex 提出 UI/UX 需求即可自动激活。例如：

```text
为我的 SaaS 产品搭建一个落地页，使用 React + Tailwind CSS，要求响应式、支持键盘操作，并先给出设计系统和页面结构，再实现代码。
```

也可以提出审查或改进请求，例如：

```text
审查当前页面的 UI/UX，重点检查视觉层次、响应式布局、可访问性和常见的模板化设计问题。
```

在提示词中明确产品类型、目标用户、视觉偏好和技术栈，通常能得到更贴合项目的结果。未指定技术栈时，skill 默认按 HTML + Tailwind CSS 提供建议。

## 4. 直接生成设计系统（可选）

如需先生成并保存设计系统，可在项目目录执行：

```bash
python3 .codex/skills/ui-ux-pro-max/scripts/search.py \
  "SaaS dashboard" \
  --design-system \
  --persist \
  -p "MyApp"
```

命令会创建类似以下结构的文件：

```text
design-system/
└── myapp/
    └── MASTER.md
```

后续让 Codex 先阅读 `design-system/myapp/MASTER.md`，再实现页面，可让颜色、字体、间距和组件规则在不同会话中保持一致。

## 5. 更新或卸载

```bash
uipro update
uipro uninstall --ai codex
```

更多平台、搜索参数和设计系统覆盖规则，请参阅项目的 [README](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill#readme)。

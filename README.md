# Skill Forge

这是一个 SK（Skill）集合仓库，用于集中维护可复用的 Codex 技能。每个技能以独立目录组织，入口文件为 `SKILL.md`，需要时可附带 `references/`、`scripts/` 或 `assets/` 等资源。

## Codex 全局约束

[`AGENTS.md`](AGENTS.md) 不是某个 SK 的项目文档，而是一套可复用的 Codex 全局约束，用于统一 Codex 的沟通、执行与测试方式。Git 相关约束由 `git` skill 统一维护。使用 Codex 时，可将 `AGENTS.md` 作为跨项目通用行为规范的参考。

使用时，可以将 `AGENTS.md` 复制到本机 Codex 全局设置目录 `~/.codex/` 下的 `~/.codex/AGENTS.md`，作为所有项目通用的约束；也可以复制到具体项目的 `.agents/` 目录下的 `<project>/.agents/AGENTS.md`，作为该项目可复用的约束。

## 技能列表

| 技能 | 说明 | 技能入口 | 安装命令 |
|---|---|---|---|
| [`code-comment-guidelines`](skills/code-comment-guidelines/SKILL.md) | 面向代码注释的编写、补充与审查，当前覆盖 C / C++、Kotlin 和 Java。 | [`SKILL.md`](skills/code-comment-guidelines/SKILL.md) | `npx skills add https://github.com/qiaozhi-4/skill-forge.git --skill code-comment-guidelines --agent codex` |
| [`git`](skills/git/SKILL.md) | 处理 Git 仓库检查、diff 阅读、提交信息和受授权的 Git 操作，默认遵循 Conventional Commits 规范。 | [`SKILL.md`](skills/git/SKILL.md) | `npx skills add https://github.com/qiaozhi-4/skill-forge.git --skill git --agent codex` |
| [`plan-documentation`](skills/plan-documentation/SKILL.md) | 规范多步骤项目计划的创建与维护，使用“主路线图 + 事项详情文档”的结构组织计划。 | [`SKILL.md`](skills/plan-documentation/SKILL.md) | `npx skills add https://github.com/qiaozhi-4/skill-forge.git --skill plan-documentation --agent codex` |

## 使用文档

- [`Spec Kit + Codex 使用文档`](docs/spec-kit-使用文档.md)：包含 Specify CLI 安装、Codex 初始化、`$speckit-*` 工作流、收敛循环和常见问题。
- [`UI UX Pro Max 使用文档`](docs/ui-ux-pro-max-使用文档.md)：安装并在 Codex 中使用 UI UX Pro Max skill 的最小流程。

## 外部技能

| 技能 | 说明 | 来源 | 安装命令 |
|---|---|---|---|
| [`karpathy-guidelines`](https://github.com/multica-ai/andrej-karpathy-skills/tree/main/skills/karpathy-guidelines) | 减少 LLM 常见编码错误的行为准则，来源于 Andrej Karpathy 的观察。 | [`multica-ai/andrej-karpathy-skills`](https://github.com/multica-ai/andrej-karpathy-skills) | `npx skills add https://github.com/multica-ai/andrej-karpathy-skills.git --skill karpathy-guidelines --agent codex` |
| [`frontend-design`](https://github.com/anthropics/skills/blob/main/skills/frontend-design/SKILL.md) | 构建或重塑 UI 时，提供有辨识度、经过刻意设计的视觉方向、字体和布局指导。 | [`anthropics/skills`](https://github.com/anthropics/skills) | `npx skills add https://github.com/anthropics/skills.git --skill frontend-design --agent codex` |

## 安装

可以使用 `skills` CLI 安装本仓库中的技能。

安装指定技能：

```bash
npx skills add https://github.com/qiaozhi-4/skill-forge.git \
  --skill code-comment-guidelines \
  --agent codex
```

安装仓库中的全部技能：

```bash
npx skills add https://github.com/qiaozhi-4/skill-forge.git --all
```

也可以使用 `--skill '*'` 匹配全部技能。

## 卸载

卸载已安装到 Codex 的指定技能：

```bash
npx skills remove code-comment-guidelines --agent codex
```

## 目录约定

```text
.
├── README.md
└── skills/
    └── <skill-name>/
        ├── SKILL.md
        └── references/
```

新增 SK 时，请保持技能目录名使用小写字母、数字和连字符，并在 `SKILL.md` 的 YAML frontmatter 中提供 `name` 与 `description`。

## 校验

可使用 `skill-creator` 提供的 `quick_validate.py` 校验单个技能：

```bash
python3 /Users/apple/.codex/skills/.system/skill-creator/scripts/quick_validate.py \
  skills/<skill-name>
```

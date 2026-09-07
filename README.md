# Skill Forge

这是一个 SK（Skill）集合仓库，用于集中维护可复用的 Codex 技能。每个技能以独立目录组织，入口文件为 `SKILL.md`，需要时可附带 `references/`、`scripts/` 或 `assets/` 等资源。

## 技能列表

### `code-comment-guidelines`

面向代码注释的编写、补充与审查，当前覆盖：

- C / C++
- Kotlin
- Java

技能入口：[skills/code-comment-guidelines/SKILL.md](skills/code-comment-guidelines/SKILL.md)

### `git-commit-messages`

生成、改写或校验符合约束的 Git 提交信息，默认遵循 Conventional Commits 规范。

技能入口：[skills/git-commit-messages/SKILL.md](skills/git-commit-messages/SKILL.md)

### `plan-documentation`

规范多步骤项目计划的创建与维护，使用“主路线图 + 事项详情文档”的结构组织计划。

技能入口：[skills/plan-documentation/SKILL.md](skills/plan-documentation/SKILL.md)

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

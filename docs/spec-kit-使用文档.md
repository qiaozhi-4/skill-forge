# Spec Kit + Codex 使用文档

本文面向使用 Codex CLI 的开发者，整理 GitHub [Spec Kit](https://github.com/github/spec-kit/) 的安装、项目初始化和规范驱动开发流程。

本文约定：

- `specify ...` 是在终端中执行的 Specify CLI 命令。
- `$speckit-*` 是在项目目录中交给 Codex 执行的技能命令，不是在普通终端中执行的 shell 命令。
- 官方文档通常写成 `/speckit.*`；Codex 的 skills 集成使用 `$speckit-*` 形式。例如，官方的 `/speckit.specify` 对应 Codex 的 `$speckit-specify`。

## 1. Spec Kit 是什么

Spec Kit 是一个规范驱动开发（Spec-Driven Development，SDD）工具集。它要求先明确“要解决什么问题、用户需要什么”，再制定技术方案、拆分任务并实现代码。

核心思想是让规范、计划和任务持续约束实现，而不是只在编码开始前写一份随后失效的文档。实现完成后，`$speckit-converge` 会再次对照规范、计划和任务检查代码，发现遗漏时把剩余工作追加回任务清单。

一个典型项目会包含以下制品：

| 制品 | 作用 |
| --- | --- |
| 项目 Constitution | 记录项目长期有效的原则、质量标准和开发约束 |
| Feature Spec | 记录某项功能的目标、用户故事、验收条件和边界 |
| Plan | 记录技术栈、架构、数据模型、接口和实现方案 |
| Tasks | 将方案拆成可执行、可排序的任务 |
| Code | 按任务实现的代码和测试 |

## 2. 安装

### 2.1 环境要求

官方安装指南目前要求：

- macOS、Linux 或 Windows；Windows 可直接使用 PowerShell，不再强制要求 WSL。
- Python 3.11 或更高版本。
- `uv`，官方推荐用于安装和管理 Specify CLI。
- Codex CLI，并确保可以在终端中正常使用。
- Git 不是核心安装的强制依赖；如果启用 Git 相关扩展，则需要 Git。

先检查本机环境：

```bash
python --version
uv --version
git --version
codex --version
```

### 2.2 安装 uv

macOS / Linux：

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Windows PowerShell：

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

安装完成后重新打开终端，确认：

```bash
uv --version
```

也可以使用 Homebrew 安装：

```bash
brew install uv
```

### 2.3 安装 Specify CLI

推荐从 PyPI 安装稳定版：

```bash
uv tool install specify-cli
```

需要固定 GitHub release 版本时，从官方仓库并指定 release tag 安装。版本号要保留开头的 `v`：

```bash
uv tool install specify-cli \
  --from git+https://github.com/github/spec-kit.git@vX.Y.Z
```

例如，实际使用时将 `vX.Y.Z` 替换为 [Spec Kit Releases](https://github.com/github/spec-kit/releases) 中存在的版本标签。

没有 `uv` 时，也可以使用其他 Python 包管理方式：

```bash
pipx install specify-cli
# 或
pip install specify-cli
```

验证 Specify CLI：

```bash
specify version
```

### 2.4 升级 Specify CLI

```bash
# 只检查是否有更新，不修改当前安装
specify self check

# 预览升级动作
specify self upgrade --dry-run

# 升级到最新稳定版
specify self upgrade

# 升级或固定到指定 release tag
specify self upgrade --tag vX.Y.Z
```

## 3. 初始化项目

初始化前先决定项目是新建还是已有项目。Spec Kit 会生成 `.specify/` 目录、模板和脚本；指定 Codex 集成后，还会生成 Codex 使用的 `.agents/skills/` 技能目录。

### 3.1 新项目

最简单的方式是让 CLI 交互式选择集成：

```bash
specify init <project_name>
```

本项目推荐明确指定 Codex 集成，并使用 Python 脚本：

```bash
specify init <project_name> --integration codex --script py
cd <project_name>
```

其中：

- `--integration codex`：为 Codex 生成 skills 集成。
- `--script py`：使用 Python 版本的 Spec Kit 辅助脚本；它不代表你的业务项目必须使用 Python。
- `cd <project_name>`：后续 `$speckit-*` 命令应在项目根目录运行。

用于 CI、脚本或无法进行交互式选择的环境时，可加 `--non-interactive`：

```bash
specify init <project_name> \
  --non-interactive \
  --integration codex \
  --script py
```

### 3.2 老项目

进入已有项目根目录后执行：

```bash
cd <existing-project>
specify init --here --force --integration codex
```

参数含义：

- `--here`：在当前目录初始化，而不是创建新的项目目录。
- `--integration codex`：生成 Codex 的 `.agents/skills/` 集成。
- `--force`：允许在已有内容的目录中继续初始化，并覆盖 Spec Kit 管理的相关文件。

已有项目中使用 `--force` 前，应确认本地没有需要保留的同名生成文件改动。它适合“明确要补齐或刷新 Spec Kit 配置”的场景，不应当作为每次执行的默认参数。

如果老项目也希望使用 Python 脚本，可以显式写出：

```bash
specify init --here --force --integration codex --script py
```

### 3.3 初始化后检查

```bash
specify version
specify integration list
```

确认项目中存在 Codex 技能：

```bash
rg --files .agents/skills | rg 'speckit'
```

初始化后，Spec Kit 的脚本通常位于以下目录之一：

- `.specify/scripts/bash/`
- `.specify/scripts/powershell/`
- `.specify/scripts/python/`

使用 `--script py` 时会生成 Python 脚本目录，同时保留平台需要的 shell fallback。

## 4. Codex 中的命令形式

启动 Codex，并确保当前工作目录是项目根目录。Codex 集成使用以下技能命令：

| 目的 | Codex 命令 | 是否通常带参数 |
| --- | --- | --- |
| 建立或更新项目原则 | `$speckit-constitution` | 默认不带；更新约束时带原则描述 |
| 创建功能规范 | `$speckit-specify` | 带功能描述 |
| 制定技术计划 | `$speckit-plan` | 默认不带；新项目或需要指定技术栈时带技术约束 |
| 生成可执行任务 | `$speckit-tasks` | 直接执行 |
| 执行实现 | `$speckit-implement` | 直接执行；大型功能可限定阶段 |
| 对照制品收敛 | `$speckit-converge` | 直接执行 |

官方文档使用 `/speckit.constitution`、`/speckit.specify` 等写法，是跨 Agent 的通用写法。使用 Codex 时，以本节的 `$speckit-*` 形式为准。

## 5. 标准开发流程

### 5.1 建立项目原则：`$speckit-constitution`

新项目第一次使用时直接执行，不需要附加内容：

```text
$speckit-constitution
```

它会建立项目后续开发都需要遵守的原则，例如代码质量、测试标准、用户体验一致性、性能、安全和目录边界。

如果后续发现项目缺少一条长期约束，可以再次运行并明确补充。例如要求所有前端页面都适配不同屏幕：

```text
$speckit-constitution 所有前端页面必须是响应式，并且至少适配 PC、平板和移动端。
```

这个命令适合记录跨功能、长期有效的规则；单个功能的特殊要求应写在 `$speckit-specify` 中。

### 5.2 创建功能规范：`$speckit-specify`

描述“要实现什么”和“为什么实现”，不要在这里决定技术栈：

```text
$speckit-specify 添加一个用户可以创建、编辑、删除和搜索项目的功能；项目列表需要显示名称、状态、更新时间，并且普通用户只能操作自己有权限的项目。
```

建议在描述中说明：

- 用户目标和使用场景。
- 主要用户故事。
- 可观察的验收条件。
- 权限、异常、空状态和边界情况。
- 明确不做的内容，避免范围不断扩大。

已有项目可以先让 Codex 阅读代码库，说明当前已实现的能力、缺失项和潜在冲突，再创建规范。例如：

```text
请先阅读当前代码库，列出与项目管理相关的已实现功能、尚未实现功能和明显缺口。暂时不要修改代码。
```

确认现状后，再运行 `$speckit-specify` 描述本次真正要补充的功能。

### 5.3 制定实施计划：`$speckit-plan`

默认直接执行，让 Codex 根据规范和当前代码库制定方案：

```text
$speckit-plan
```

新项目或希望明确技术栈时，再把技术选择作为参数传入。例如：

```text
$speckit-plan 前端使用 React 和 Vite，后端使用 FastAPI，数据存储使用 PostgreSQL，优先复用现有依赖并保持 API 分层清晰。
```

技术栈、架构、数据模型、接口、测试策略和技术约束属于 Plan 阶段；功能的用户价值和行为仍属于 Specify 阶段。

### 5.4 生成可执行任务：`$speckit-tasks`

直接执行：

```text
$speckit-tasks
```

它会根据规范和计划生成任务清单，并按依赖关系拆分基础工作、用户故事和收尾工作。生成后建议快速检查任务是否覆盖每个用户故事及其验收条件。

### 5.5 执行实施：`$speckit-implement`

直接执行全部任务：

```text
$speckit-implement
```

对于较大的功能，可以按阶段或用户故事分批执行，例如：

```text
$speckit-implement 只实现 Setup 和 Foundational 阶段，完成项目脚手架、数据模型和基础 CRUD，先不要实现后续用户故事。
```

每一批实现后先运行项目已有的测试、构建和必要的手工检查，再进入下一批。

### 5.6 评估并收敛：`$speckit-converge`

实施完成后直接执行：

```text
$speckit-converge
```

它会把代码与 Feature Spec、Plan 和 Tasks 对照，检查是否存在遗漏、偏差或未完成任务。该命令不会修改业务代码；发现缺口时，会将剩余工作追加到 `tasks.md` 的 Convergence 区域。

结果有两种：

- `Converged`：规范、计划和任务已基本得到实现，可以进入代码审查或提交 PR。
- `Tasks appended`：发现剩余工作，需要再次执行 `$speckit-implement`，然后再次执行 `$speckit-converge`。

## 6. 本项目推荐的完整循环

### 6.1 新项目首次开发

```text
specify init <project_name> --integration codex --script py
cd <project_name>

$speckit-constitution
$speckit-specify <描述要实现的功能>
$speckit-plan
$speckit-tasks
$speckit-implement
$speckit-converge
```

如果 `converge` 发现缺口，继续循环：

```text
$speckit-implement
$speckit-converge
```

直到 `converge` 报告 `Converged`。

### 6.2 老项目增加功能

```text
cd <existing-project>
specify init --here --force --integration codex
```

然后按功能执行：

```text
$speckit-specify <描述要增加或修改的功能>
$speckit-plan
$speckit-tasks
$speckit-implement
$speckit-converge
```

如果项目还没有可用的 Constitution，先执行一次：

```text
$speckit-constitution
```

### 6.3 日常规则

总体流程可以记成：

```text
全局约束：constitution
功能规范：specify
技术方案：plan
执行任务：tasks
实现代码：implement
对照收敛：converge

implement -> converge -> implement -> converge
```

`implement -> converge` 是一个循环，直到没有剩余任务；新的功能则从 `specify` 开始，不要直接跳到实现。

## 7. 可选质量关卡

官方还提供几个可选命令。功能越复杂、需求越模糊，越值得加入这些步骤：

| 命令 | 使用时机 | 作用 |
| --- | --- | --- |
| `$speckit-clarify` | Specify 后、Plan 前 | 针对不明确的需求提出问题并回写规范 |
| `$speckit-checklist` | 实现前 | 生成需求质量清单，检查完整性、清晰度和一致性 |
| `$speckit-analyze` | Tasks 后、Implement 前 | 只读检查 Spec、Plan、Tasks 之间的矛盾、缺口和覆盖关系 |
| `$speckit-taskstoissues` | 需要 GitHub 跟踪时 | 将任务清单转换为 GitHub Issues |

复杂功能可以使用以下增强流程：

```text
$speckit-constitution
$speckit-specify <功能描述>
$speckit-clarify
$speckit-plan
$speckit-checklist
$speckit-tasks
$speckit-analyze
$speckit-implement
$speckit-converge
```

如果质量关卡发现问题，应回到拥有该问题的阶段修复：需求问题回到 `$speckit-specify` 或 `$speckit-clarify`，设计问题回到 `$speckit-plan`，任务问题回到 `$speckit-tasks`。

## 8. 扩展和预设

Spec Kit 的核心流程之外，还可以安装扩展或预设：

```bash
# 搜索扩展
specify extension search

# 安装扩展
specify extension add <extension-name>

# 搜索预设
specify preset search

# 安装预设
specify preset add <preset-name>
```

通常：

- 需要增加新命令、新工作流或外部工具集成时使用 Extension。
- 需要改变规范、计划或任务模板，强制组织规范或术语时使用 Preset。
- 安装社区扩展或预设前，应先审阅其源代码和模板内容。

## 9. 常见问题

### `specify: command not found`

确认 Specify CLI 已安装并且 `uv` 的 tool bin 目录已加入 `PATH`：

```bash
uv tool list
uv tool install specify-cli
specify version
```

安装或修改 `PATH` 后重新打开终端。

### Codex 中找不到 `$speckit-*`

确认：

1. Codex 当前打开的是项目根目录，而不是项目的上级目录或子目录。
2. 项目中存在 `.agents/skills/`。
3. 初始化时使用了 `--integration codex`。
4. `specify integration list` 能看到 Codex 集成。

如果项目初始化不完整，可以在确认要刷新 Spec Kit 生成文件后重新执行：

```bash
specify init --here --force --integration codex --script py
```

### `$speckit-*` 在终端里无法执行

这是预期行为。`$speckit-*` 是 Codex skill，不是 shell 命令；应在 Codex 对话中运行。终端只执行 `specify ...` 命令。

### `--script py` 会不会把项目变成 Python 项目？

不会。它只决定 Spec Kit 生成的辅助脚本类型，和业务项目使用的语言、框架无关。

### 什么时候可以跳过完整流程？

纯文字、拼写、非常小的局部修复可以直接按普通开发流程处理。涉及新功能、跨文件修改、数据模型、接口、权限、页面交互或较大重构时，建议至少使用 `specify -> plan -> tasks -> implement -> converge`。

## 10. 官方参考

- [Spec Kit GitHub 仓库](https://github.com/github/spec-kit/)
- [Spec Kit 中文 README](https://github.com/github/spec-kit/blob/main/README.zh-CN.md)
- [官方安装指南](https://github.com/github/spec-kit/blob/main/docs/installation.md)
- [官方 Agentic SDD 参考](https://github.github.io/spec-kit/reference/agentic-sdd.html)
- [支持的 AI 编码助手集成](https://github.github.io/spec-kit/reference/integrations.html)
- [Specify CLI 参考](https://github.github.io/spec-kit/reference/overview.html)
- [uv 安装文档](https://docs.astral.sh/uv/getting-started/installation/)

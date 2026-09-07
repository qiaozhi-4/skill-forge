# Conventional Commits 1.0.0（摘要）

来源：https://www.conventionalcommits.org/en/v1.0.0/

## 格式

```
<type>[optional scope][optional !]: <description>

[optional body]

[optional footer(s)]
```

## 类型

必须是名词，例如：`feat`、`fix`、`docs`、`style`、`refactor`、`perf`、`test`、`build`、`ci`、`chore`、`revert` 等。

- `feat` → SemVer 的次版本
- `fix` → SemVer 的修订版本
- 破坏性变更 → SemVer 的主版本

## 作用域（Scope）

可选的括号内名词，例如：`feat(parser):`、`fix(api):`。

## 描述（Description）

- 紧跟在 `: ` 之后。
- 简要概括变更内容。
- 使用祈使语气和现在时态；中文应使用简洁的动宾短语。
- 不要求首字母大写，但应保持风格一致。
- 末尾不加句号。

## 正文（Body）

- 与 description 之间空一行。
- 格式自由，可以包含多个段落。
- 说明变更动机，以及与之前行为的差异。

## 页脚（Footer）

- 与正文之间空一行。
- 使用 token 加 `:` 或 `#`，后接对应值。
- token 中用 `-` 代替空格，例如 `Reviewed-by`。
- 特殊情况：`BREAKING CHANGE: <变更说明>`（也可以在 type 或 scope 后使用 `!`）。

## 示例

```
feat: 允许提供的配置对象扩展其他配置

BREAKING CHANGE: 配置文件中的 `extends` 键现在用于扩展其他配置文件
```

```
feat(lang): 增加波兰语支持
```

```
fix: 防止请求竞争

引入请求 ID 和最新请求的引用。忽略除最新请求之外的
其他响应。

移除原本用于缓解竞争问题、但现在已经过时的超时机制。

Reviewed-by: Z
Refs: #123
```

```
docs: 修正 CHANGELOG 的拼写
```

```
feat!: 产品发货后向客户发送邮件
```

```
feat(api)!: 产品发货后向客户发送邮件
```

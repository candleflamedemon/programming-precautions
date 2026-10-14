# 编程注意事项 / Programming Precautions

一个用于 AI 辅助本地编程的 Codex skill：在运行、构建、测试或变更依赖时，为每个项目维护唯一的项目内隔离环境，并用不可覆盖的版本化 PDF 记录环境状态。

A Codex skill for AI-assisted local development. It maintains one project-local isolated environment for running, building, testing, and dependency changes, with immutable versioned PDF environment reports.

## 中文

### 解决的问题

AI 编程工具为了验证代码，常会在本机安装依赖或直接运行程序。如果复用全局 Python、Conda `base` 或共享工具链，可能造成依赖冲突和环境污染；如果反复创建多个虚拟环境，又会产生冗余并让当前环境难以判断。

本 skill 规定：

- 每个项目只维护一个活动的独立编译/运行环境。
- 默认将环境放在项目根目录的 `compile-environment/runtime/`。
- 优先复用现有环境；发现多个候选环境时暂停并让用户选择，不擅自删除。
- 不把依赖安装到全局 Python、Conda `base` 或其他共享环境。
- 每次环境发生实际变化，都生成新的版本化 PDF，旧版永不覆盖。
- PDF 记录工具链、依赖、命令、验证结果和重建步骤，同时排除秘密值和不必要的私人路径。

### 适用场景

在 AI 工具需要执行以下本地操作时使用：

- 运行程序或脚本；
- 构建、编译或测试项目；
- 安装、删除或升级依赖；
- 更换解释器、编译器、SDK 或包管理器；
- 调整影响运行的环境配置和命令。

纯代码阅读、仅编辑文件或完全在远程 CI 中执行时，不需要创建本地环境。

### 安装

将本仓库克隆或复制到 Codex skills 目录：

```text
<CODEX_HOME>/skills/programming-precautions/
```

目录中应直接包含 `SKILL.md`、`agents/` 和 `references/`。

### 使用

可让 Codex 自动识别适用任务，也可以显式调用：

```text
Use $programming-precautions to prepare and document the isolated local environment for this project.
```

环境默认结构：

```text
compile-environment/
|-- runtime/
|-- 编译环境说明_v1.0.0_初始创建.pdf
`-- 编译环境说明_v1.0.1_更新测试依赖.pdf
```

报告命名格式：

```text
编译环境说明_vMAJOR.MINOR.PATCH_简要修改内容.pdf
```

- `MAJOR`：不兼容的运行时/工具链替换或整体重建。
- `MINOR`：新增或移除主要能力、SDK 或依赖模块。
- `PATCH`：依赖版本、配置、命令或报告内容的小幅修正。

### 真实限制

- 不同语言生态对项目内工具链、缓存和依赖目录的支持程度不同；强制位于常规目录的文件需要在 PDF 中如实说明。
- 本 skill 不会在发现多个既有环境时自动删除或合并它们。
- PDF 生成依赖当前 Codex 环境中可用且可信的 PDF 生成能力，但不得为此创建第二个项目环境。
- 用户明确选择其他环境策略时，Codex 会先说明隔离性、唯一性或可回溯性影响，再按用户决定执行。

## English

### Problem addressed

AI coding tools often install dependencies or execute code locally for verification. Reusing a global Python installation, Conda `base`, or a shared toolchain can cause conflicts and pollution. Creating multiple virtual environments for the same project introduces redundancy and makes the active environment ambiguous.

This skill enforces the following invariants:

- Maintain exactly one active isolated build/runtime environment per project.
- Store it at `compile-environment/runtime/` by default.
- Reuse an existing environment; if multiple candidates are found, stop and ask the user which one to keep or migrate.
- Never install dependencies into global Python, Conda `base`, or another shared environment.
- Generate a new versioned PDF after every actual environment change; never overwrite older reports.
- Record toolchains, dependencies, commands, validation results, and reconstruction steps without exposing secrets or unnecessary private paths.

### When to use

Use this skill when an AI tool needs to perform local execution, builds, compilation, tests, dependency changes, toolchain upgrades, or environment configuration changes.

Do not create an environment for read-only code review, file-only edits, or workflows executed entirely in remote CI.

### Installation

Clone or copy this repository into the Codex skills directory:

```text
<CODEX_HOME>/skills/programming-precautions/
```

`SKILL.md`, `agents/`, and `references/` should be located directly inside that directory.

### Usage

Codex may select the skill automatically, or it can be invoked explicitly:

```text
Use $programming-precautions to prepare and document the isolated local environment for this project.
```

The default project layout is:

```text
compile-environment/
|-- runtime/
|-- 编译环境说明_v1.0.0_初始创建.pdf
`-- 编译环境说明_v1.0.1_更新测试依赖.pdf
```

Report names use `编译环境说明_vMAJOR.MINOR.PATCH_简要修改内容.pdf`. Major versions cover incompatible toolchain replacement or full rebuilds, minor versions cover new capability groups, and patch versions cover smaller dependency, configuration, command, or report corrections.

### Limitations

- Language ecosystems differ in how completely their toolchains, caches, and dependencies can be redirected into a project folder. Required exceptions must be documented in the PDF.
- The skill does not automatically delete or merge existing environments when more than one is detected.
- PDF creation relies on a trusted PDF capability available to Codex and must not introduce a second project environment.
- If the user explicitly chooses another environment strategy, Codex explains the effect on isolation, uniqueness, and traceability before proceeding.

## License

MIT License. See [LICENSE](LICENSE).

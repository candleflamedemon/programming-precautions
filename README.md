# 编程注意事项 / Programming Precautions

一个用于 AI 辅助编程的 Codex skill：编程前确认是否生成结果说明报告，以集中自定义变量、详细中文注释和功能分块提高可复核性，并在本地运行、构建、测试或变更依赖时维护唯一的项目内隔离环境及版本化 PDF 环境说明。

A Codex skill for AI-assisted development. Before implementation it asks whether to create a result report, organizes user-configurable values, Chinese comments, and functional sections for reviewability, and maintains one project-local isolated environment with immutable versioned PDF environment reports.

## 中文

### 解决的问题

AI 编程工具为了验证代码，常会在本机安装依赖或直接运行程序。如果复用全局 Python、Conda `base` 或共享工具链，可能造成依赖冲突和环境污染；如果反复创建多个虚拟环境，又会产生冗余并让当前环境难以判断。

本 skill 规定：

- 创建或修改程序前，先确认用户是否需要编程结果说明报告；需要时详细记录最终程序与真实运行结果。
- 把用户可调整的参数集中到开头的“自定义变量区”或权威配置模块，并为其提供中文说明。
- 为新写或修改的自有代码增加准确的中文注释和清晰的功能分块，同时避免无意义的逐行复述。
- 每个项目只维护一个活动的独立编译/运行环境。
- 默认将环境放在项目根目录的 `compile-environment/runtime/`。
- 优先复用现有环境；发现多个候选环境时暂停并让用户选择，不擅自删除。
- 不把依赖安装到全局 Python、Conda `base` 或其他共享环境。
- 每次环境发生实际变化，都生成新的版本化 PDF，旧版永不覆盖。
- PDF 记录工具链、依赖、命令、验证结果和重建步骤，同时排除秘密值和不必要的私人路径。

### 适用场景

在 AI 工具需要执行以下本地操作时使用：

- 创建或修改可执行源代码；
- 运行程序或脚本；
- 构建、编译或测试项目；
- 安装、删除或升级依赖；
- 更换解释器、编译器、SDK 或包管理器；
- 调整影响运行的环境配置和命令。

纯代码阅读、只做解释或完全在远程 CI 中执行时不启用本 skill。只编辑源代码时仍应用报告确认和代码可复核性规则，但不因编辑本身创建本地环境。

### 安装

将本仓库克隆或复制到 Codex skills 目录：

```text
<CODEX_HOME>/skills/programming-precautions/
```

目录中应直接包含 `SKILL.md`、`agents/` 和 `references/`。

### 使用

可让 Codex 自动识别适用任务，也可以显式调用：

```text
Use $programming-precautions to prepare this coding task, ask about a result report, and keep the local environment isolated.
```

开始编程时，Codex 会先询问是否生成“编程结果说明报告”。如果回答“需要”但未指定格式和路径，默认生成：

```text
docs/编程结果说明_yyyyMMddHHmm.md
```

报告包含程序目的、架构、文件与功能分块、自定义变量、输入输出、运行方法、实际运行命令与结果、验证结论和已知限制。未执行、失败或跳过的检查会如实标明，不会被写成成功结果。

推荐的单文件代码组织方式如下；具体注释语法会适配所用语言：

```text
导入与语法必需声明

===== 自定义变量区 =====
用户可修改的路径、阈值、开关等

===== 功能分块：输入处理 =====
实现与中文注释

===== 功能分块：核心计算 =====
实现与中文注释

===== 功能分块：结果输出 =====
实现与中文注释
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
- 代码注释和功能分块会尊重项目既有架构与格式；不会机械改写第三方、生成、压缩或供应商代码。
- 编程结果报告只在用户选择需要时生成，并且只能记录实际观察到的运行证据。
- 用户明确选择其他环境策略时，Codex 会先说明隔离性、唯一性或可回溯性影响，再按用户决定执行。

## English

### Problem addressed

AI coding tools often install dependencies or execute code locally for verification. Reusing a global Python installation, Conda `base`, or a shared toolchain can cause conflicts and pollution. Creating multiple virtual environments for the same project introduces redundancy and makes the active environment ambiguous.

This skill enforces the following invariants:

- Ask whether the user wants a programming result report before creating or changing executable source code.
- Place user-adjustable settings in an opening customization section or the authoritative configuration module, with Chinese guidance.
- Add accurate Chinese comments and clear functional section boundaries to newly written or modified first-party code.
- Maintain exactly one active isolated build/runtime environment per project.
- Store it at `compile-environment/runtime/` by default.
- Reuse an existing environment; if multiple candidates are found, stop and ask the user which one to keep or migrate.
- Never install dependencies into global Python, Conda `base`, or another shared environment.
- Generate a new versioned PDF after every actual environment change; never overwrite older reports.
- Record toolchains, dependencies, commands, validation results, and reconstruction steps without exposing secrets or unnecessary private paths.

### When to use

Use this skill when an AI tool creates or modifies executable source code, or performs local execution, builds, compilation, tests, dependency changes, toolchain upgrades, or environment configuration changes.

Do not invoke it for read-only review, explanation-only work, or workflows executed entirely in remote CI. Source-only edits still use the report decision and code-reviewability rules, but do not create an environment merely because files changed.

### Installation

Clone or copy this repository into the Codex skills directory:

```text
<CODEX_HOME>/skills/programming-precautions/
```

`SKILL.md`, `agents/`, and `references/` should be located directly inside that directory.

### Usage

Codex may select the skill automatically, or it can be invoked explicitly:

```text
Use $programming-precautions to prepare this coding task, ask about a result report, and keep the local environment isolated.
```

Before implementation, Codex asks whether to generate a programming result report. If requested without a custom format or path, it writes a versioned Markdown report under `docs/`. The report covers the final program in detail and records only observed commands, tests, outputs, failures, skipped checks, and limitations.

User-configurable values are kept near the beginning of the program, after syntax-required headers and imports. First-party code uses Chinese comments and language-appropriate section markers to separate configuration, input handling, core logic, and output behavior.

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
- Comment and section rules adapt to the repository's established architecture and do not mechanically rewrite generated, vendored, third-party, or minified code.
- Programming result reports are opt-in and must distinguish observed results from expected or unexecuted behavior.
- If the user explicitly chooses another environment strategy, Codex explains the effect on isolation, uniqueness, and traceability before proceeding.

## License

MIT License. See [LICENSE](LICENSE).

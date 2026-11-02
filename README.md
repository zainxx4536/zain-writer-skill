# Zain Writer｜中文文档编写与修订

一个用于中文学习资料、论文详解、代码讲解、实验报告及实施文档的 AI Skill。围绕读者和用途组织内容，保留必要解释与真实依据，使成品便于独立阅读、使用和复述。

## 两种工作模式

| 模式 | 适用任务 | 核心要求 |
|---|---|---|
| 修改完善已有文档 | 纠错、补缺、精简、重组、续写 | 保留准确清楚的内容和用户编辑，按授权调整结构与表述 |
| 根据资料新编文档 | 根据论文、代码、笔记、数据或实验材料编写新稿 | 按用途选材、整合和解释，形成完整主线，核对内容与来源 |

模式按本次任务判断。同一篇详解可以先根据论文新编，再进入局部修订；另存为新文件也可能仍是修订任务。

## 完整规范与读取顺序

[writing-guide.md](references/writing-guide.md) 全文保留了用于制作本技能的通用 Prompt，包含格式、请求澄清、两种工作类型、结构、行文措辞、不同用途、素材、事实依据及交付要求。

每次执行文档任务时，先完整读取这份规范，再按任务读取 A 或 B 模式的操作补充。`SKILL.md` 是入口，模式文件补充操作顺序；二者都不能代替完整规范，也不能删弱其中的要求。

后续维护以完整规范为依据，同步调整入口和模式补充，避免将已确认的具体要求重新压缩成含义更宽泛的概括句。

## 默认约定

- **格式**：未指定输出格式时使用 Markdown（`.md`）。明确指定其他格式或要求保留原格式时，按本次要求执行。
- **图片**：保留与正文有关的图片，放在成品文档同级的 `asset` 文件夹中，用相对路径嵌入，并核对实际图像与引用。
- **公式**：Markdown 数学公式使用 `$$...$$`，起止 `$$` 分别独占一行；解释必要变量并核对显示。
- **澄清**：目标稿、范围、结构取舍或覆盖方式等重要决定不明确时，先询问，确认后再执行相关工作。

成品目录示例：

```text
学习资料/
├── 论文详解.md
└── asset/
    └── 方法流程图.png
```

“先评估”“先规划”“只反馈文字”等阶段要求优先执行。任务已经清楚并获授权时，直接完成，不额外增加确认步骤。

## 安装到 Codex

这是纯指令型技能，不需要为它额外安装 Python、Node.js 或依赖包。模型调用、文件读写，以及 PDF、Word、PPT 等文件处理能力由宿主环境提供。

以下 Windows PowerShell 示例使用目录联接，使全局技能入口指向本地源码；拉取更新后无需再复制一份。需要已安装 Git，并配置好 GitHub SSH 访问。

先克隆仓库并进入目录：

```powershell
git clone git@github.com:zainxx4536/zain-writer-skill.git
Set-Location -LiteralPath '.\zain-writer-skill'
```

在仓库根目录创建全局入口：

```powershell
$writerSource = (Resolve-Path -LiteralPath '.').Path
$writerCodexHome = if ($env:CODEX_HOME) {
    $env:CODEX_HOME
} else {
    Join-Path $env:USERPROFILE '.codex'
}
$writerSkillRoot = Join-Path $writerCodexHome 'skills'
$writerLink = Join-Path $writerSkillRoot 'zain-writer'

if (Test-Path -LiteralPath $writerLink) {
    throw "技能入口已存在，请先检查它指向的位置：$writerLink"
}
New-Item -ItemType Directory -Path $writerSkillRoot -Force | Out-Null
New-Item -ItemType Junction -Path $writerLink -Target $writerSource
```

安装后，在下一轮对话中使用 `$zain-writer`。如果宿主尚未刷新技能列表，可重新打开对话或重启宿主。创建联接后请保留本地源码目录，不要将其移动或删除。

在不支持目录联接的环境中，可把本仓库内容放入该宿主支持的技能目录，并将技能文件夹命名为 `zain-writer`。尚未安装时，也可在任务中直接指定本地 `SKILL.md` 的路径。

## 使用示例

修改已有文档：

```text
请使用 $zain-writer 修改这篇学习笔记。
只补充第二章缺少的过程说明，保留第一、三章和已有代码，另存为修订版.md。
```

根据资料新编文档：

```text
请使用 $zain-writer，根据所附论文和代码写一篇面向初学者的详解。
按论文的主要结构组织，讲清概念、公式、完整流程，以及代码与论文的对应和差异。
```

只做评估：

```text
请使用 $zain-writer，先评估这份实验报告的结构和结果分析。
只反馈文字，暂不修改文件。
```

每次仍需提供可访问的资料、目标稿及本次约束。技能不依赖历史聊天，也不要求把个人写作记录加入仓库。

## 文件结构

```text
zain-writer/
├── SKILL.md                         # 技能入口、必读顺序与模式选择
├── agents/openai.yaml               # 界面信息与自动选择设置
├── references/
│   ├── edit-existing.md             # A 模式操作补充
│   ├── draft-from-sources.md        # B 模式操作补充
│   └── writing-guide.md             # 通用 Prompt 全文，所有模式必读
├── README.md
└── LICENSE
```

默认允许宿主按任务匹配此技能，也可明确调用。普通简短答疑和业务代码开发不在它的主要适用范围内。

## 更新与验证

采用上述目录联接安装时，在源码目录更新即可：

```powershell
git status --short
git pull --ff-only
```

拉取前先检查并处理自己的未提交修改。

初版已完成技能结构、元数据和内部链接检查，以及 5 次新编 Markdown 试写、局部修订、请求澄清和只反馈文字场景的验证。试写覆盖了块级公式、`asset` 图片引用、输入保护和指定章节保留；没有据此声称所有文档类型和编辑器均已验证。

完整规范改版另核对了与源 Prompt 的全文一致性、入口与模式补充的读取关系，并通过了 A/B 两种模式的试用检查：局部修订保留指定章节和代码，新编稿正确处理公式与图片引用。完整规范不依赖仓库外的文件，克隆或安装本仓库即可获得全部要求。

Word、PPT、PDF 的读取与编辑仍依赖宿主工具；公式实际显示应在目标阅读器中检查。技能提供写作流程和约束，不能替代资料核实、专业判断或实验验证。

## 许可证

本仓库自有的技能指令、参考文件与说明文档采用 [MIT License](LICENSE)，允许按许可证条件使用、修改与分发。

用户处理的论文、教材、代码、数据和图片仍受各自的授权条件约束，本许可证不为外部输入材料提供额外授权。仓库不包含个人聊天记录、课程资料或试写输入。

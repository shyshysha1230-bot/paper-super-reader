## Paper Super Reader v1.1.0

一个基于开放 Agent Skills `SKILL.md` 结构的可移植 Skill 包。默认中文，读取用户提供的论文 PDF；可选择先用 MinerU 转录为 Markdown 再阅读（非必需，不配置第三方 API 时可直接读 PDF）。分析后输出 Markdown 报告。

### 分阶段阅读模式

默认只做用户指定的部分。用户没有指定部分时，先做 `quick` 概览，再询问想继续读哪一部分；不默认一次性生成完整报告。

| 模式 | 做什么 | 适合怎样使用 |
|---|---|---|
| `transcribe` | 用 MinerU 将 PDF 转录为 `00-原文转录.md`（与报告同目录），供后续阅读与复跑；已有则复用。未配置 MinerU 时可跳过。 | “先转录这篇论文” |
| `full` | 分阶段完整精读：先做阶段一概览，再按用户指定的章节做阶段二深读。只有用户明确要求时才把阶段二所有部分都做完。 | “先概览，再精读引言和方法” |
| `quick` | 阶段一：三句话总结、章节结构、初步贡献判断、5–10 个关键词。 | “先快速了解这篇论文” |
| `intro` | 只深读引言：背景、已知/未知、研究缺口、研究问题/假设、研究逻辑链。 | “只分析引言” |
| `methods` | 只深读方法；按论文类型分析设计、样本/材料、流程、测量、分析方法等。 | “重点看研究设计和变量测量” |
| `results` | 只深读结果：逐项说明研究发现、分析方法、原文统计证据、图表及其回答的问题。 | “逐条解读结果和统计值” |
| `discussion` | 只深读讨论与结论：作者如何解释结果、文中如何比较已有研究、意义、局限、未来方向与结论闭环。 | “只看讨论和结论” |
| `figures` | 只读 PDF 中的图表：解释每张图表的元素、主要模式、正文对应关系和解读边界。 | “按顺序讲解所有 Figure” |

可自然组合，例如“先 quick，然后只做 methods 和 results”。`full` 不会自动代表审稿式批判、外部文献比较或记忆练习；这些属于其它工作流，其提示词已作为**预留 asset**（批判评价/关联拓展/知识巩固/应用输出）随包提供，供后续功能复用，当前默认不调用。

### 阅读纪律

- 语言正式、准确、简洁，避免营销式/情绪化/夸张措辞。
- 用自己的语言概括，不大段照抄原文；图表不简单复述标题，而说明其支持了哪些结论、与正文的关系。
- 默认离线，不引用论文之外的信息；确需外部信息时单列“外部扩展”并标明来源。
- 缺失信息写“论文未报告 / 待核验”，不猜测、不以外知补全；术语首次出现用“中文（English）”，之后保持一致。

### 安装方式

ZIP 内部目录名为 `paper-super-reader/`。将 ZIP 解压到对应 Skills 目录即可：

**OpenCode**

```bash
mkdir -p ~/.config/opencode/skills
unzip ~/Downloads/Paper-Super-Reader-v1.zip -d ~/.config/opencode/skills
```

也可放在 OpenCode 支持的 agent-compatible 目录 `~/.agents/skills/`。

**Codex**

```bash
mkdir -p ~/.agents/skills
unzip ~/Downloads/Paper-Super-Reader-v1.zip -d ~/.agents/skills
```

**Claude Code**

```bash
mkdir -p ~/.claude/skills
unzip ~/Downloads/Paper-Super-Reader-v1.zip -d ~/.claude/skills
```

**ChatGPT 或 Claude 网页端**

如果账号/工作区提供自定义 Skills 上传功能，可在对应 Skills 设置界面上传此 ZIP。网页产品的可用性、入口和账号权限由平台控制；不同平台的显式调用方式也不同。可以直接用自然语言描述“使用 Paper Super Reader，只分析方法部分”。

安装后如果平台没有自动识别 Skill，按该平台说明刷新或重新启动应用。

### 输出与保存

- 默认报告语言：中文；关键术语可在首次出现时附英文原词。
- 元数据（标题、作者、年份、期刊、DOI、简短标题等）以论文首页为准；无法确认的字段写“待核验”，不联网猜测。
- 用户指定保存路径时优先采用；未指定时，若平台可访问并写入上传 PDF 所在文件夹，则默认将报告保存为 `.md` 文件。
- 若上传 PDF 所在位置不可写或平台只提供临时附件访问，则在对话中返回 Markdown 内容并说明无法保存到 PDF 文件夹。
- 启用转录时，转录文本默认保存为 `00-原文转录.md`，与报告同目录；原 PDF 作为底稿不移动、不复制、不改名。
- 不覆盖同名报告；遇到重名时追加序号或先询问用户。

### 目录约定

- `SKILL.md`：跨平台通用的触发信息、工作流、约束与阅读纪律。
- `references/`：论文类型分析规则、证据边界、转录规则（`transcription.md`）与元数据规则（`metadata.md`）。
- `assets/`：不同模式使用的 Markdown 报告模板；含 4 个**预留 asset**（`critical-appraisal.md`、`related-extension.md`、`consolidation.md`、`application-reports.md`），当前默认不调用。

### Paper Skills Suite 开发计划

本次 V1 **只交付 `paper-super-reader`**。下面三个 Skill 是已确定的后续开发计划，当前尚未包含在本 ZIP 中；README 记录它们的分工和目标文件结构，避免与 Reader 重复建设。

> **v1.1.0 说明**：上述阶段三/四/五（以及综述/汇报/提案应用）的核心提示词，已先行以**预留 asset** 形式内置到 `paper-super-reader/assets/`（`critical-appraisal.md`、`related-extension.md`、`consolidation.md`、`application-reports.md`），供后续功能直接复用，**当前精读流程默认不调用**。这属于过渡安排，不改变下表独立 Skill 的长期规划。

| 计划 Skill | 对应原工作流 | 核心问题 | 与 Reader 的边界 |
|---|---|---|---|
| `paper-critical-appraiser` | 阶段三：批判性评价 | 这篇论文的设计、证据和结论有多可信？ | 重点核对方法是否足以支持 claim；不重复写一遍论文摘要。 |
| `paper-comparator` | 阶段四：关联与跨文献比较 | 多篇论文之间有哪些共识、冲突、关系和研究缺口？ | 比较用户提供的多篇 PDF；只有用户要求时才做外部文献发现。没有可用搜索能力时，请用户提供候选 PDF，不要求单独配置 API。 |
| `paper-memory-builder` | 阶段五：知识巩固与记忆 | 如何把论文内容转换成可复习、可提取的知识？ | 从 Reader 报告或上传论文制作记忆材料；避免重新生成一份普通摘要。 |

#### `paper-critical-appraiser/` 计划结构

```text
paper-critical-appraiser/
├── SKILL.md
├── references/
│   ├── appraisal-framework.md
│   ├── design-specific-appraisal.md
│   ├── claim-evidence-alignment.md
│   └── causal-inference.md
└── assets/
    ├── full-appraisal.md
    ├── rapid-appraisal.md
    └── focused-appraisal.md
```

分析论文类型对应的质量要点，评价研究问题、理论、设计、测量、统计、偏差、替代解释、结果可信度和结论边界。使用“claim ladder”对齐作者主张与研究设计可支持的证据强度，并区分作者承认的局限和额外的分析判断。

#### `paper-comparator/` 计划结构

```text
paper-comparator/
├── SKILL.md
├── references/
│   ├── comparison-framework.md
│   ├── evidence-synthesis.md
│   └── source-handling.md
└── assets/
    ├── multi-paper-comparison.md
    ├── seed-paper-comparison.md
    └── theory-comparison.md
```

支持“用户上传多篇 PDF 进行比较”和“以一篇种子论文寻找比较对象”两种模式。用证据矩阵比较问题、理论、样本、设计、测量、分析、发现和局限，再综合共识、冲突、差异来源及研究缺口。外部文献检索依赖宿主平台已有的搜索能力；不内置要求用户配置的 API Key。

#### `paper-memory-builder/` 计划结构

```text
paper-memory-builder/
├── SKILL.md
├── references/
│   ├── active-recall.md
│   ├── question-design.md
│   └── concept-compression.md
└── assets/
    ├── memory-pack.md
    ├── one-page.md
    ├── mind-map.md
    └── quiz.md
```

把论文或已有精读报告转换成“一句话主旨、三条要点、核心模型、思维导图、一页纸、自测题与答案、易混淆概念”等主动回忆材料。题目用于理解和迁移，不只考术语记忆。

#### 共享设计约定

- 三个 Skill 都遵守 Reader 的论文类型适配、来源忠实、来源/推断/外部信息区分和 Markdown 输出规则。
- 每个 Skill 可独立使用；有 Reader 报告时可将其作为输入，但不强制用户先运行 Reader。
- `references/` 只存分析规则；`assets/` 只存输出模板。
- 每个 Skill 均支持独立安装；跨 Skill 调用语法按宿主平台适配，不写死为某个平台的 `@`、`$` 或 slash command。
- 不开发 `paper-to-presentation` 或 `paper-to-project`；这两类任务由用户已有工具负责。

#### 建议开发顺序

1. 用实际 PDF 验证 `paper-super-reader` 的分阶段流程和跨平台安装体验。
2. 开发 `paper-critical-appraiser`，复用论文类型分类与证据边界规则。
3. 开发 `paper-comparator`，先完成多篇用户上传 PDF 的比较，再扩展到宿主平台可用的外部检索。
4. 开发 `paper-memory-builder`，支持直接读取 PDF，也支持接收 Reader/Appraiser/Comparator 的 Markdown 报告。

完成后可将四个独立 Skill 合并成一个 Suite 分发包；当前 ZIP 仍仅包含 `paper-super-reader/`。

核心结构遵循开放 Agent Skills 规范。各平台的文件访问能力、安装入口、自动触发和上传 PDF 后的保存权限有所不同，因此包格式可移植，但平台功能并非完全一致。

### 版本记录

- **v1.1.0**（当前）：新增可选 `transcribe`（MinerU 转录）流程与 `references/transcription.md`；新增 `references/metadata.md`（元数据规则）与“阅读纪律”；`paper-types.md` 补充“判定依据清单”；新增 4 个预留 asset（批判评价/关联拓展/知识巩固/应用输出）；明确当前精读流程不做完整批判评价（预留待用）；README 同步。
- **v1.0.0**：首次发布 `paper-super-reader`（阶段一概览 + 阶段二深读；11 类论文类型规则与证据边界）。

### 官方格式参考

- [Agent Skills standard](https://agentskills.io/)
- [OpenCode Skills](https://opencode.ai/docs/skills/)
- [Codex Skills](https://developers.openai.com/codex/skills)
- [Claude Agent Skills](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

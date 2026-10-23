# Requested Change 1 修改计划与逐段对照

更新时间：2026-10-23。

## 一、范围、文件和当前状态

本计划针对 **RC1：纠正 SEDAR 的 comment-before-mutation and restore-after-mutation 描述，并准确解释 MORPH 与 SEDAR 的方法差异**。在修改稿中，应着重对该研究的技术贡献及其与既有工作之间的关系，给出更准确且论据充分的阐述。

**本轮仅调整本文件。下文均为后续修改建议，不表示正文、回复信或图片已经修改。**

执行时以必要的局部纠正为主，保留原有的问题动机、方法主线和实验结果。重点是补全恢复步骤、纠正由临时注释推导出的逻辑丢失结论，并具体说明 MORPH 的贡献。

| 文件 | 用途 | 处理原则 |
| --- | --- | --- |
| `../MajorRevisonBack.tex` | 原始提交版本、最终 diff 基准 | 保持不变，包括文件名中的拼写 |
| `../main.tex` | clean 正文 | 你逐项认可后，只实施对应单项；本轮不改 |
| `../responseletter.tex` | 回复信 | 依据实际完成的修改另行确认更新；本轮不改 |
| `MajorRevision.md` | 评审意见与已有 rebuttal | 作为要求及已有实验陈述的来源 |
| 本文件 | 修改计划 | 记录拟改内容、证据核查和验收事项 |

RC2 负责更完整的创新性、能力表及其他相关工作讨论；RC4 负责 SEDAR 实现来源和可复现性。本计划保留与这两项的衔接，不将它们全部展开为 RC1 的改写任务。

## 二、建议采用的工作顺序

1. 对照 RC1 和相关 A/B 评论定位问题，在第四节保留中文说明，在第七节保留对应的英文原文与拟修改源码。
2. 由你逐项审阅并明确认可某一项；未认可的建议继续保留在计划中，不批量应用到 clean。
3. 只落实你认可的该项，核对修改范围及结果；涉及案例、图或实验数据时，先满足该项列出的核实条件。
4. 该项实际实施并核对后，从第四节和第七节删除对应条目，更新相关待办。其余条目和编号保持不变；仅认可方案但尚未实施时不删除。
5. 回复信根据已完成修改另行确认更新。最后核对全文一致性，从冻结原稿与最终 clean 生成提交用高亮 diff。

## 三、本条修改的核心立场

### RC1 必须落实的内容

- **补全 SEDAR 的恢复步骤。** 临时注释发生在变异前，恢复发生在变异后、目标 DBMS 执行前。相应纠正将中间隐藏形式当成最终执行查询的文字和图示。
- **保留并讲清 MORPH 的方法贡献。** 差异包括 AQT 对结构及前向读后写依赖的显式表示、目标端模式和类型上下文、方言引导实例化，以及解析/执行反馈修复。不能将全部贡献收缩为“修复时机不同”，也不能依赖 SEDAR 永久删除查询逻辑的错误描述。
- **保留具体动机案例并准确解释失败。** 补全恢复后的 SQL 和执行结果，依据实际记录说明该例为什么失败。案例结论限定于观察到的现象。

### 执行时的写作与证据原则

- **保留正向实验结论。** 覆盖率、漏洞数和有效 SQL 特征计数可以支持完整流程的实用效果；删除将差异直接归因于 SEDAR 永久删除片段的句子即可。对单个环节的归因由相应对照实验支持，无须在各结果段反复写限制声明。
- **区分迁移和变异两个阶段。** MORPH 的常量替换和最小范围注释可能改变迁移种子的行为，可在兜底策略处简短说明一次。“两种策略均不保证变异后语义等价”用于直接回答 Reviewer B 的对应问题，不在引言、图注、方法和结果中反复补写。
- **控制改写范围。** 保留翻译有效性、多语句依赖一致性和下游 fuzzer 兼容性的动机。SEDAR 的完整机制在背景处集中介绍，其他位置按需简述或引用。不将“隐藏片段不能参与语法变异”新增为核心挑战；若需要分析该点，应结合实际变异器和证据。
- **保留准确内容。** 不因处理 RC1 而整段删除有用的方法解释、将具体 SQL 案例换成泛化流程图，或批量清理与本问题无关的源码注释。

## 四、逐项修改计划及中文前后对照

以下沿用原计划的条目编号，便于核对位置；英文原文与拟修改源码见第七节同号条目。编号不代表每条都要产生正文改动；源码注释的处理属于按需整理。你认可并实施一项后，再删除本节与第七节的对应条目。

### 2. Introduction：SEDAR 介绍与两项挑战

**修改前：** 最近面向模糊测试的方法利用 LLM 进行端到端测试用例迁移。例如，SEDAR 把模式信息和原始 SQL 文本输入 LLM，生成目标方言种子。但这类方法有两个根本局限：第一，对目标方言理解有限、对多语句结构感知较弱，可能引入语法或语义错误，导致无效或不一致的查询行为；第二，为满足下游模糊测试器严格的语法约束，通常删除或注释不支持的结构进行清理，可能简化 SQL 结构并削弱执行行为。

**建议修改后：** 最近面向模糊测试的方法利用 LLM 在 DBMS 之间迁移已有测试用例。例如，SEDAR 把源端模式信息和 SQL 语句输入 LLM，生成目标方言种子。然而，迁移仍面临两个挑战：第一，翻译需要协调目标方言约束与多语句测试中的模式引用和类型依赖，否则可能引入语法错误或执行错误；第二，迁移后的种子还需适配下游模糊测试器的语法约束。SEDAR 通过在变异前临时注释解析器不支持的片段，并在变异后、目标 DBMS 执行前恢复它们来缓解解析不兼容；该机制本身不修复翻译错误。

**修改尺度：** 保留“已有方法 → 两项挑战 → MORPH”的原有结构，优先局部修改。补上恢复步骤，撤掉由注释推导永久结构简化的结论即可，不展开 SEDAR 的全部处理流程，也不新增一段免责声明。

**引用：** SEDAR 的具体机制对应引用 SEDAR。QTRAN 的任务和机制在 RC2 中单独核对，避免将不同工作笼统归入同一处理方式。

### 3. Introduction：旧方法总结注释

**原注释要点：** 保留与测试有关的 SQL 特征，同时避免直接文本翻译引起的翻译失败和破坏性清理。

**计划：** 保留结构与测试特征的动机；若该注释将 SEDAR 描述为永久破坏逻辑，只修改这部分比较。无须为 RC1 删除整句正向方法说明。

### 4. Background 写作指导：旧失败链注释

**原注释要点：** SEDAR 幻觉 → 注释式清理 → 逻辑丢失。

**计划：** 更正为“翻译结果 → 变异前临时注释 → 变异 → 恢复 → 实际执行结果”。具体失败原因依据案例记录补充。

### 5. Cross-Dialect Seed Transfer：SEDAR 与 MORPH 的区别

**修改前：** SEDAR 等工作探索基于 LLM 的种子迁移。虽然翻译完整测试用例提升了自动化程度，但迁移中仍没有显式保留跨语句依赖。

**建议修改后：** SEDAR 利用源端模式信息迁移完整测试用例，并通过变异前临时注释、变异后恢复处理下游解析器不兼容。MORPH 通过 AQT 显式表示测试结构和前向读后写依赖，结合持续更新的目标端模式与类型上下文进行实例化，并利用解析和执行反馈修复迁移种子。

**修改尺度：** 在这里集中说明完整机制与主要差异。保留跨语句协调这一贡献，不将 SEDAR 概括为完全没有多语句处理能力。更完整的创新性比较与 RC2 统一。

### 6. Cross-Dialect Seed Transfer 后：仅提注释的备用注释

**计划：** 若保留该备用文字，补上变异后恢复；若与正式正文重复，可删除。无需新增专门的对比段落。

### 7. Motivating Example：两个备用开头注释

**原注释要点：** 使用 SEDAR 的具体案例说明跨方言种子迁移的局限。

**计划：** 这两句本身没有机制性错误，可保留；是否去重按最终行文需要决定，不列为 RC1 必改项。

### 8. Motivating Example：开头正文

**修改前要点：** LLM 迁移可能引入错误；fuzzer 语法有限；注释不支持特征会造成结构简化。

**建议修改后：** 接下来通过一个 PostgreSQL 到 DuckDB 的测试用例，说明跨方言迁移中的翻译错误与下游解析器兼容性挑战。测试包含时间谓词及类型转换。SEDAR 在变异期间临时注释解析器不支持的片段，并在执行前恢复；下文结合恢复后的查询和执行结果分析该例。

**修改尺度：** 保留具体案例及其动机作用，纠正“注释必然造成最终逻辑丢失”的解释。具体 SQL 名称和类型与原图、记录核对。

### 9. 动机图本体：保留具体 SQL，补全恢复步骤

**原图：** `media/motivating_71.pdf`，使用具体 SQL 展示迁移过程。

**计划：** 优先沿用原案例和 SQL 对照布局，在 SEDAR 路径补上恢复步骤及恢复后的实际 SQL、执行结果。将临时注释形式标为变异用的中间状态。MORPH 一侧保留对应 AQT 抽象、目标方言实例化和必要修复的具体操作。

**修改尺度：** 原计划以两个泛化流程框替换 SQL 图的方案不再采用。可为恢复步骤调整布局，但不以流程说明取代具体案例。保留 `fig:motivation` 标签；如修改图片，使用新素材文件，避免影响冻结原稿。

**待核对：** 恢复后的实际 SQL、错误信息，以及 MORPH 对应输出。只展示记录支持的处理过程。

### 10. 动机图：图注

**修改前要点：** SEDAR 存在语义错误和结构简化；MORPH 实例化等价的 date_diff 逻辑。

**建议修改后：** PostgreSQL 到 DuckDB 的测试用例迁移示例：左侧展示 SEDAR 的翻译、临时注释、恢复及执行结果；右侧展示 MORPH 的 AQT 抽象、目标方言实例化与反馈修复。

**修改尺度：** 图注描述图中内容，实际失败原因在正文说明。“等价”仅在该例经核实时使用；不在图注追加变异后语义等价的通用限制。

### 11. 现有方法局限：修正案例因果解释

**修改前要点：** 注释 AGE、将 WHERE 条件替换为 True，使最终查询丢失验证逻辑，无法触及深层状态。

**建议修改后（核对 trace 后定稿）：** 在该动机案例中，SEDAR 暂时注释解析器不支持的片段以进行变异。执行前恢复片段后，查询仍因翻译结果中的不兼容结构产生执行错误，影响该测试用例的有效执行。

**修改尺度：** 保留原段分析案例失败的功能，补上恢复后的真实情况，删除“临时隐藏直接导致最终逻辑丢失”的错误因果。无需把小标题改成限制声明，或再用数句解释该例不能证明什么。

**待核对：** rebuttal 已陈述恢复后出现错误，仍需用原始记录确认具体 SQL、错误及原因；不能仅凭 AGE 名称断言目标 DBMS 不支持它。

### 12. 动机案例的核查记录

**计划：** 将原始输入、翻译结果、变异/恢复结果和执行反馈保存在实验材料中，并在本计划跟踪。无需为此在正文源码新增三条说明性注释。

### 13. Basic Idea of MORPH

**保留内容：** 结构抽象与目标方言实例化的核心思路、案例中的 TimeDiff 表示，以及保留有用测试结构的设计目标。

**局部调整：** 将“不会进行错误翻译”“确保有效”“必然到达更深路径”等保证性措辞改为设计目标或实测结果；仅在当前案例确实涉及时保留窗口分区说明。

**建议表述主线：** MORPH 将方言相关操作抽象为 AQT 节点，结合目标方言和类型信息实例化，并利用解析与执行反馈修复不兼容结构，生成保留有用测试结构的可执行种子。

**修改尺度：** 保留方法解释的具体性与正向动机，不将整段改为修复时机比较，也不追加变异后等价性声明。

### 14. 反馈驱动节：对比指导注释

**原注释要点：** 注释是最后兜底，与 SEDAR 式默认清理拉开距离。

**计划：** 保留 MORPH 的兜底顺序说明；去掉将 SEDAR 概括为默认破坏性清理的比较。

### 15. 反馈驱动节：变异前处理流程

**原文要点：** 迁移种子经过兼容性检查、目标端执行和必要修复，并标记可变异区域。

**计划：** 原流程说明保留。若时机不清，仅补明修复后的种子交给下游 fuzzer 变异；检查、执行、重试与标记的顺序按实现核对。

**修改尺度：** 本节重点解释 MORPH 如何使用反馈修复，不重复展开 SEDAR 的流程或语义等价性问题。

### 16. 反馈驱动节：旧英文备用段落注释

**原注释要点：** 将 SEDAR 的处理称为破坏性注释，同时描述 AQT 节点定位、反馈修复和删除多余括号的案例。

**计划：** 仅更正错误的 SEDAR 比较；AQT 定位和具体修复案例可保留。备用段落若与正文重复，可按需删除。

### 17. 四级兜底策略结尾

**保留顺序：** 已有映射 → RAG 候选 → 源端执行后常量替换 → 最小范围注释。

**建议局部修改：** 只有前面的修复尝试失败后，才采用最小范围注释。常量替换和最小范围注释可能改变迁移种子的行为。

**修改尺度：** 保留“尽量先修复、最后兜底”的设计理由；在此说明一次迁移阶段的行为变化即可，不延伸为各节重复的免责声明。

### 18. RQ2 漏洞数量结果的解释

**修改前要点：** 漏洞数差异被归因于 SEDAR 通过破坏性清理牺牲语义深度，而 MORPH 保留完整逻辑并触及基线无法到达的路径。

**建议修改后：** 这些结果表明，MORPH 生成的迁移种子能够有效支持后续模糊测试，在所评估的运行中获得更高覆盖率并发现更多缺陷。其结构感知迁移结合目标端上下文和反馈修复，为下游 fuzzer 提供可执行且包含丰富 SQL 结构的种子。

**修改尺度：** 保留数字、结果比较和实际效用解释。去掉 SEDAR 永久删除逻辑的错误归因，以及“本次未发现”等同于“无法触及”的推断。单个兼容性处理环节的贡献由下一项实验讨论，无须在本段反复解释完整流程比较的限制。

### 19. RQ2：纳入已有变异处理对照实验

**来源：** 已有 rebuttal 的 Reviewer B Question 2。

**拟纳入内容：** 使用相同的 300 个翻译后种子和 GRIFFIN，比较两种处理策略的 24 小时运行。已有 rebuttal 报告 MORPH 在 DuckDB、MonetDB 上分别获得 27.2%、15.5% 的分支覆盖率提升。

**用途：** 直接回应共同下游 fuzzer 下两种处理策略效果的疑问。正文报告设置与结果，语义等价性问题在对应回复中回答。

**定稿前核实：** 共用种子的含义和清单、两种策略实现、DBMS/fuzzer 版本、重复次数、资源及覆盖统计口径、原始覆盖值和百分比算法。与另一个 100/200/300 种子数量控制实验分别报告；不擅自补写运行次数或显著性。

### 20. 对照实验的核查记录

**计划：** 数据来源和待核实配置留在本计划及实验材料中。原计划新增多条正文源码注释的安排取消；无需在论文中解释本轮是否新运行了实验。

### 21. RQ3：SEDAR 基线流程描述

**修改前：** 我们复现了 SEDAR 的完整流程，包括模式捕获、端到端 LLM 翻译以及注释式清理策略。

**建议局部修改：** 补全流程名称为“源端模式捕获、LLM 翻译，以及变异前临时注释、变异后且目标执行前恢复”。

**核对要求：** 确认所评估的实现确实包含恢复步骤。“复现”或“使用原始二进制”等来源表述由 RC4 按实际情况统一，不能仅通过改写流程说明解决来源问题。

### 22. RQ3：特征多样性的旧 TODO 注释

**计划：** 去掉将临时注释直接解释为最终特征丢失的归因。可保留 MORPH 通过目标兼容实例化与反馈修复提供丰富变异输入的说明。

### 23. RQ3：特征计数差距的解释

**修改前要点：** 将多样性差距主要归因于 SEDAR 注释无法映射的结构。

**建议修改后：** 有效迁移输出中的函数、类型和关键字计数表明，MORPH 能够生成包含更丰富 SQL 特征的种子。其方言引导的 AQT 实例化与反馈修复旨在使复杂结构适配目标端和下游 fuzzer。

**修改尺度：** 保留特征数字及其反映的种子丰富度，删除错误的永久丢失归因。计数阶段和“有效输出”的定义按统计脚本核实，不在此重复完整流程无法隔离单个环节的说明。

### 24. Related Work：LLM-Based Seed Generation

**保留内容：** SEDAR 是最接近的工作；MORPH 以显式结构和依赖支持有上下文的实例化与局部修复。

**计划：** 对 SEDAR 的说明补全临时注释和恢复机制，按篇幅可引用背景介绍。MORPH 的区别保留 AQT、前向读后写依赖、目标端上下文和反馈修复，不缩减为单纯的前后时机差异。

**修改尺度：** 准确的原段落保留，仅调整不准确的比较。其他翻译系统及更完整的创新性边界在 RC2 统一讨论，本段不重复案例失败和变异后语义等价性限制。

## 五、回复信对应计划

| 回复位置 | 后续回应要点 | 定稿要求 |
| --- | --- | --- |
| Requested Change 1 | 准确说明已纠正的机制、动机案例和 MORPH 实际方法差异，概述对照实验 | 正文落实后再写完成式，给出位置和证据 |
| Comment 2.3 | 简要确认机制纠正，引用 RC1 | 避免重复完整回复 |
| Comment 2.8 | 说明流程修正及共同 GRIFFIN 对照 | 核实实验设置和结果 |
| Comment 2.14 | 直接说明两者均不保证变异后语义等价；解释实际优势并引用对照实验 | 区分迁移种子等价率与变异后等价率 |
| Comment 1.8 | 回答其中的评估、恢复步骤及复现问题 | 不能用 RC1 交叉引用替代全部问题 |
| Requested Change 4 | 单独说明 SEDAR 实现来源、取得方式和修改 | 按实际来源核实，不在 RC1 中提前声称完成 |

回复保留评审原文和顺序，说明实际修改内容与位置，按需引用原句和新句。局限问题直接回答一次，其他重复问题交叉引用。

## 六、后续执行与验收清单

当前仅更新计划，以下正文与回复信事项均待实际落实和核验后勾选。

- [x] 调整 RC1 计划为必要的局部纠错，保留原有论证主线。
- [x] 取消以泛化流程框替代具体 SQL 案例的建议。
- [x] 合并重复限制声明，区分迁移阶段行为变化与变异后语义等价。
- [x] 恢复第七节的逐项英文源码对照，明确每项经你认可后单独实施。
- [ ] 按你认可的单项修改 clean，核对后删除第四节和第七节对应条目。
- [ ] 在正文中补全 SEDAR 的注释、变异、恢复和执行顺序。
- [ ] 修正图和文字中由临时注释推导永久逻辑丢失的内容。
- [ ] 保留并明确 AQT、前向读后写依赖、目标端上下文及反馈修复的贡献。
- [ ] 核实动机案例原始 SQL、恢复后的查询和执行反馈，补全具体案例图。
- [ ] 核实并纳入已有 300 种子处理策略对照实验，保留设置和结果。
- [ ] 核实特征计数的阶段及口径，保留其对种子丰富度的正向解释。
- [ ] 在兜底策略处简短说明可能的行为变化，在对应回复中回答变异后等价性。
- [ ] 与 RC2 的创新性讨论及 RC4 的实现来源说明统一。
- [ ] 根据实际修改更新回复信，核对完成式、位置和证据。
- [ ] 检查引用、数字、图表和页数，从冻结原稿生成最终高亮 diff。

## 七、英文原文与修改后源码完整对照

以下各项与第四节编号对应，保留当前 clean 中完整的待替换片段及拟修改源码，供逐项审阅。**“修改后”均为拟稿，尚未应用；你认可哪一项，再只实施该项。** 条件未核实的项先保留，不将拟稿视为已确认的事实。

每项实施并核对后，同时从第四节和本节移除该项，更新相关待办；其他编号保持不变。本文不提供自动应用全部改动的补丁。提交用高亮 diff 仍由冻结原稿与最终 clean 生成。

### 7.2 Introduction：SEDAR 介绍与两项挑战

**状态与范围：** 仅该段的拟稿。QTRAN 的任务与引用归属在 RC2 单独核对。

**修改前（当前 clean）：**

```latex
Recent fuzzing-oriented approaches instead leverage large language models (LLMs) for end-to-end test-case transfer~\cite{fu2024sedar, lin2025qtran}. For example, \sedar{}~\cite{fu2024sedar} combines schema information and raw SQL text as input to an LLM to generate target-dialect seeds. However, such approaches still suffer from two fundamental limitations:
(i) First, due to limited understanding of target SQL dialects and weak structural awareness of multi-statement programs, LLM-based translation may introduce syntactic or semantic errors during generation, leading to invalid or inconsistent query behaviors.
(ii) Second, to satisfy strict grammar constraints imposed by downstream fuzzers, generated test cases are often sanitized by removing or commenting out unsupported constructs, which may simplify complex SQL structures and weaken execution behaviors.
```

**修改后（拟稿，尚未应用）：**

```latex
Recent fuzzing-oriented approaches instead leverage large language models (LLMs) to transfer existing test cases across DBMSs~\cite{fu2024sedar}. For example, \sedar{}~\cite{fu2024sedar} combines source-schema information and SQL statements as input to an LLM to generate target-dialect seeds. However, transferring high-quality fuzzing seeds still poses two challenges:
(i) First, translation must coordinate target-dialect constraints with schema references and type dependencies across statements; inconsistencies may introduce syntax or execution errors.
(ii) Second, transferred seeds must also satisfy the grammar constraints imposed by downstream fuzzers. \sedar{} temporarily comments out parser-incompatible fragments before mutation and restores them afterward, before executing the mutated SQL on the target DBMS; this compatibility mechanism does not itself repair translation errors.
```

### 7.3 Introduction：旧方法总结注释

**状态与范围：** 源码注释的可选更正，不影响 PDF；保留正向方法解释。

**修改前（当前 clean）：**

```latex
% This enables fuzzing to retain testing-relevant SQL features while avoiding the translation failures and destructive sanitization caused by direct text translation.
```

**修改后（拟稿，尚未应用）：**

```latex
% This enables fuzzing to retain testing-relevant SQL features through structure-aware instantiation and feedback-guided repair of transfer incompatibilities.
```

### 7.4 Background：旧失败链注释

**状态与范围：** 可选源码注释更正。

**修改前（当前 clean）：**

```latex
% - Motivating example 必须写成失败过程：SedaR hallucination -> comment-out sanitization -> logic loss。
```

**修改后（拟稿，尚未应用）：**

```latex
% - Motivating example：展示翻译结果 -> 临时注释 -> 变异 -> 恢复 -> 实际执行结果；失败原因以案例记录为准。
```

### 7.5 Cross-Dialect Seed Transfer：实际方法差异

**状态与范围：** 集中说明 SEDAR 的机制和 MORPH 的主要差异；其余相关工作按 RC2 处理。

**修改前（当前 clean）：**

```latex
Recent work such as \sedar{}~\cite{fu2024sedar} explores fuzzing-oriented seed transfer using LLMs. While it improves automation by translating complete test cases, it still does not explicitly preserve cross-statement dependencies during migration.
```

**修改后（拟稿，尚未应用）：**

```latex
\sedar{}~\cite{fu2024sedar} transfers complete test cases using source-side schema information and handles downstream parser incompatibilities through temporary commenting before mutation and restoration before target-side execution.
\tool{} explicitly represents test structures and forward read-after-write dependencies in an AQT, coordinates instantiation with evolving target-side schema and type context, and uses parser and execution feedback to repair transferred seeds.
```

### 7.6 Cross-Dialect Seed Transfer：备用注释

**状态与范围：** 可选更正；若最终决定删除重复注释，可只删除该行。

**修改前（当前 clean）：**

```latex
% To improve fuzzer compatibility, it applies unparsable commenting to handle parsing errors by commenting out incompatible parts or statements.
```

**修改后（拟稿，尚未应用）：**

```latex
% To improve fuzzer compatibility, it temporarily comments out parser-incompatible fragments before mutation and restores them afterward, before target-side execution.
```

### 7.7 Motivating Example：两个备用开头注释

**状态与范围：** 原文可保留，当前不建议改动。若确认保留，可直接移出待改清单，无须修改 clean。

**修改前（当前 clean）：**

```latex
% In the following, we illustrate with a concrete example the limitations of \sedar{} in cross-dialect seed transfer.
% We illustrate this gap using \sedar{}, the closest existing fuzzing-oriented seed-transfer system.
```

**修改后（拟稿，尚未应用）：**

```latex
% In the following, we illustrate with a concrete example the limitations of \sedar{} in cross-dialect seed transfer.
% We illustrate this gap using \sedar{}, the closest existing fuzzing-oriented seed-transfer system.
```

### 7.8 Motivating Example：开头正文

**状态与范围：** 待与原图和原始 SQL 核对具体案例内容后定稿。

**修改前（当前 clean）：**

```latex
We next present a motivating example to illustrate the limitations of LLM-based fuzzing-oriented seed transfer methods such as \sedar{}.
LLM-based cross-dialect transfer methods help in the generation of initial fuzzer seeds but may introduce \textit{syntax or semantic errors} due to hallucinations.
Moreover, many fuzzers cover only a subset of SQL grammar.
Existing methods typically comment out unsupported features to support seed mutation after transfer, which alters the query and causes \textit{structural simplification}.
```

**修改后（拟稿，尚未应用）：**

```latex
We next present a PostgreSQL-to-DuckDB test case to illustrate translation and fuzzer-compatibility challenges in cross-dialect seed transfer.
The test contains a temporal predicate and type casts.
\sedar{} temporarily comments out parser-incompatible fragments during mutation and restores them before execution.
We examine the restored query and its execution result to explain the failure in this example.
```

### 7.9 动机图：图片引用与具体 SQL

**状态与范围：** 待确认：保留具体 SQL 案例并补全恢复阶段。motivating_rc1.pdf 是拟用的新素材文件名，目前未制作，不可直接应用这行替换。先形成可审核的新图，再确认图片引用的修改；本项不包含图注，图注见第 10 项。

**修改前（当前 clean）：**

```latex
  \includegraphics[width=\linewidth]{media/motivating_71.pdf}
```

**修改后（拟稿，尚未应用）：**

```latex
  \includegraphics[width=\linewidth]{media/motivating_rc1.pdf}
```

### 7.10 动机图：备用图注和正式图注

**状态与范围：** 待与补全后的案例图核对。该项一并移除含错误归因的备用图注，保留图标签和其他 figure 代码。

**修改前（当前 clean）：**

```latex
  % \caption{Motivating example: \sedar{} causes semantic deviation and logic loss, while \tool{} preserves intent via AQT.}
  \caption{Conventional end-to-end translation methods like \sedar{} suffer from semantic error and structural simplification (Left). \tool{} decouples intent via AQT, successfully instantiating the equivalent \texttt{date\_diff} logic (Right).}
```

**修改后（拟稿，尚未应用）：**

```latex
  \caption{A PostgreSQL-to-DuckDB test-case transfer example. Left: \sedar{}'s translation, temporary commenting, restoration, and execution result. Right: \tool{}'s AQT abstraction, target-dialect instantiation, and feedback-guided repair.}
```

### 7.11 动机案例：局限解释

**状态与范围：** 待核实：原始恢复后 SQL、错误信息及其原因。核实前保留为拟稿，不应用到 clean。

**修改前（当前 clean）：**

```latex
\textit{Limitations of Existing Approaches.} Figure \ref{fig:motivation} illustrates the semantic deviation and logic loss \sedar{} experiences when transferring a test case from \pg{} to \duckdb{}, which contains dialect-specific features like the \texttt{AGE} function in the \texttt{WHERE} clause and the \texttt{::INTERVAL} type cast. With limited knowledge of \duckdb{}’s syntax, the LLM hallucinates and preserves \texttt{AGE}, causing syntax and semantic errors. To satisfy parsing constraints, \sedar{} comments out \texttt{AGE} and replaces the \texttt{WHERE} condition with \texttt{True}, producing a syntactically valid but simplified query that loses the original verification intent and fails to exercise deep internal states.
As a result, the migrated seed has limited utility for subsequent effective mutation in DBMS fuzzing.
```

**修改后（拟稿，尚未应用）：**

```latex
\textit{Limitations of Existing Approaches.} In the PostgreSQL-to-DuckDB example in Figure~\ref{fig:motivation}, \sedar{} temporarily comments out parser-incompatible fragments to enable mutation and restores them before target-side execution.
In the observed case, the restored query still encounters an execution error caused by an incompatibility in the translated construct, preventing this test case from executing successfully.
```

### 7.12 动机案例：核查记录

**状态与范围：** 原文无对应的新增核查注释；不向 clean 添加说明性注释，记录留在本计划和实验材料中。

**修改前：** 当前 clean 无对应新增内容。

**修改后：** 保持 clean 不变；该项仅保留核查安排。

### 7.13 Basic Idea of MORPH

**状态与范围：** 待核对图中的 AQT 标签和实际操作。保持具体方法解释，去掉不会出错、必然到达更深路径等保证性推断。

**修改前（当前 clean）：**

```latex
The core idea of \tool{} is to thoroughly decouple logical intent from syntactic representation, shifting from unstable textual translation to more robust structural recursive substitution.
As shown in Figure~\ref{fig:motivation}, \tool{} does not perform erroneous syntactic translation, but instead abstracts the dialect-specific \texttt{AGE} function into a generic $<$\texttt{TimeDiff}$>$ intent.
It then performs a high-fidelity instantiation into \duckdb{}’s equivalent syntax. By ensuring the type casting and window function partition logic are preserved, \tool{} generates a high-fidelity seed that retains the original filtering complexity.
By maintaining these complex semantics during transfer, \tool{} ensures the generated seeds are effective for validation, enabling them to reach deeper code paths within the DBMS and uncover intricate logic bugs that might be unreachable by \sedar{}.
```

**修改后（拟稿，尚未应用）：**

```latex
The core idea of \tool{} is to decouple structural information from dialect-specific syntax through an AQT, enabling structure-aware target-dialect instantiation.
As shown in Figure~\ref{fig:motivation}, \tool{} abstracts the time-difference operation into a generic $<$\texttt{TimeDiff}$>$ label and instantiates it using target-side dialect and type information.
Parser and execution feedback then guide the repair of remaining incompatibilities.
This process aims to produce executable, fuzzer-compatible seeds while retaining useful structures and the filtering complexity of the test case.
```

### 7.14 反馈驱动节：对比指导注释

**状态与范围：** 可选源码注释更正。

**修改前（当前 clean）：**

```latex
% - 强调 comment out 是最后兜底，和 SedaR 式默认 sanitization 拉开距离。
```

**修改后（拟稿，尚未应用）：**

```latex
% - 强调最小范围 comment out 是最后兜底，保留此前映射、检索和修复的优先顺序。
```

### 7.15 反馈驱动节：处理时机

**状态与范围：** 待按实现核对检查、执行、重试和标记的顺序；仅明确本节流程，不重复 SEDAR 机制说明。

**修改前（当前 clean）：**

```latex
As shown in Figure~\ref{fig:feedback-driven}, compatible seeds are sanitized, executed in the target DBMS, repaired when necessary, and annotated with mutable regions for fuzzing.
```

**修改后（拟稿，尚未应用）：**

```latex
As shown in Figure~\ref{fig:feedback-driven}, transferred seeds are checked against the fuzzer parser, executed in the target DBMS, repaired when necessary, and annotated with mutable regions before downstream mutation.
```

### 7.16 反馈驱动节：旧英文备用注释

**状态与范围：** 可选源码注释更正，仅去掉对 SEDAR 的错误比较；具体修复说明保留。

**修改前（当前 clean）：**

```latex
% Specifically, when a transferred seed $Q_{target}$ is fed into the fuzzer, it may still contain subtle syntax incompatibilities not captured by the static translation phase. Instead of discarding these seeds or destructively commenting out unparsable tokens (as in \sedar{}~\cite{fu2024sedar}), the sanitizer captures the parser error message, localizes the unsupported dialect-specific construct to the corresponding AQT node, and repairs it using syntactic feedback. For instance, in Figure~\ref{fig:feedback-driven}, it automatically removes excess brackets around the \texttt{date\_diff} function to produce a syntactically valid Mutable Seed.
```

**修改后（拟稿，尚未应用）：**

```latex
% Specifically, when a transferred seed $Q_{target}$ is fed into the fuzzer, it may still contain subtle syntax incompatibilities not captured by the static translation phase. Instead of discarding these seeds, the sanitizer captures the parser error message, localizes the unsupported dialect-specific construct to the corresponding AQT node, and repairs it using syntactic feedback. For instance, in Figure~\ref{fig:feedback-driven}, it automatically removes excess brackets around the \texttt{date\_diff} function to produce a syntactically valid Mutable Seed.
```

### 7.17 四级兜底策略结尾

**状态与范围：** 保留前文四级顺序；仅在此说明迁移种子的行为可能变化。

**修改前（当前 clean）：**

```latex
Thus, destructive commenting is used only after structure-preserving repairs fail.
```

**修改后（拟稿，尚未应用）：**

```latex
Thus, minimal commenting is used only after earlier repair attempts fail.
Constant substitution and minimal commenting may change the behavior of transferred seeds.
```

### 7.18 RQ2：漏洞数量结果解释

**状态与范围：** 保留前面的漏洞数量、表格及比较结果；不改实验数字。

**修改前（当前 clean）：**

```latex
This proves that coverage gains are not merely statistical but functionally impactful, enabling the detection of bugs that remain unreachable to baselines.
Unlike \sedar{}, which sacrifices semantic depth through destructive sanitization, \tool{} employs structure-aware transfer to retain the full logical complexity of source test cases. By preserving executable semantics during transfer, \tool{} enables fuzzers to bypass early engine rejections, exercise deeper code logic, and trigger more bugs.
```

**修改后（拟稿，尚未应用）：**

```latex
These results demonstrate the practical utility of the transferred seeds for downstream fuzzing, yielding higher branch coverage and more detected bugs in the evaluated campaigns.
\tool{} combines structure-aware transfer, target-side context, and feedback-guided repair to provide executable seeds containing rich SQL structures.
```

### 7.19 RQ2：已有变异处理对照实验

**状态与范围：** 待核实：已有 rebuttal 的种子清单、实现、版本、重复次数、资源、覆盖原始值和算法。当前 clean 无此段；拟插入 RQ2 漏洞数量结果解释之后。与 100/200/300 种子数量控制实验分别处理。

**修改前：** 当前 clean 无对应新增内容。

**修改后（拟稿，尚未应用）：**

```latex
\textbf{Controlled Comparison of Mutation Handling.}
To compare the handling strategies under the same downstream fuzzer, we sampled the same 300 translated seeds and applied the two strategies using \griffin{} for 24 hours.
With \tool{}'s pre-mutation repair, branch coverage was 27.2\% higher on DuckDB and 15.5\% higher on MonetDB than with \sedar{}'s comment-and-restore handling.
```

### 7.20 对照实验：核查注释

**状态与范围：** 原文无对应新增注释；不向 clean 添加数据来源或“本轮是否新运行”的说明性源码注释。

**修改前：** 当前 clean 无对应新增内容。

**修改后：** 保持 clean 不变；该项仅保留核查安排。

### 7.21 RQ3：SEDAR 基线流程

**状态与范围：** 待核实所评估实现确实包含恢复。We reproduced 暂按原文保留；来源措辞及取得方式由 RC4 按实际情况统一，本条拟稿不代表来源核实完成。

**修改前（当前 clean）：**

```latex
We reproduced \sedar{}'s complete pipeline, including schema capture, end-to-end LLM translation, and its comment-out sanitization strategy.
```

**修改后（拟稿，尚未应用）：**

```latex
We reproduced \sedar{}'s complete pipeline, including source-schema capture, LLM-based translation, temporary commenting before mutation, and restoration after mutation and before target-side execution.
```

### 7.22 RQ3：特征多样性的旧 TODO

**状态与范围：** 可选源码注释更正；不改变特征计数结果。

**修改前（当前 clean）：**

```latex
% \TODO{The diversity gap mainly comes from sanitization strategy: \sedar{} may comment out unparsable casts or function calls, whereas \tool{} constructively instantiates abstract structures into target-compliant forms, preserving more feature-rich inputs for downstream mutation.}
```

**修改后（拟稿，尚未应用）：**

```latex
% \TODO{Explain how AQT-guided instantiation and feedback repair provide target-compatible, feature-rich seeds; do not attribute the feature-count difference to permanent removal of temporarily hidden fragments in \sedar{}.}
```

### 7.23 RQ3：特征计数差距解释

**状态与范围：** 待核实计数阶段和有效输出定义；保留前文数字及表格。

**修改前（当前 clean）：**

```latex
The diversity gap mainly comes from how each baseline handles unmappable constructs: statement-level translators raise errors or omit them, while \sedar{} comments them out, whereas \tool{} constructively instantiates abstract structures into target-compliant forms via retrieved dialect evidence, preserving more feature-rich inputs for mutation.
```

**修改后（拟稿，尚未应用）：**

```latex
The counts of valid functions, data types, and keywords show that \tool{} produces seeds with richer SQL features.
Its dialect-guided AQT instantiation and feedback repair aim to adapt complex structures to the target DBMS and downstream fuzzer.
```

### 7.24 Related Work：LLM-Based Seed Generation

**状态与范围：** 与背景和 RC2 的术语统一；不扩展为对其他系统的笼统归纳。

**修改前（当前 clean）：**

```latex
\sedar{}~\cite{fu2024sedar} is the closest work to \tool{} because it also transfers existing DBMS test cases into fuzzing seeds.
However, these methods largely rely on direct text generation or post-generation filtering and sanitization.

In contrast, \tool{} performs AQT-guided whole-test-case instantiation, where explicit schema dependencies, nested structures, and clause-level relationships support local repair while preserving context.
```

**修改后（拟稿，尚未应用）：**

```latex
\sedar{}~\cite{fu2024sedar} is the closest work to \tool{} because it also transfers existing DBMS test cases into fuzzing seeds.
It uses source-side schema information for translation and temporarily hides parser-incompatible fragments during mutation, restoring them before target-side execution.

In contrast, \tool{} performs AQT-guided test-case instantiation with explicit forward read-after-write dependencies and evolving target-side context, supporting parser- and execution-feedback repair of transferred structures.
```

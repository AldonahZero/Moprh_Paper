# Requested Change 1 修改计划与逐段对照

更新时间：2026-10-22。

## 一、范围、文件和当前状态

本计划针对 **RC1：纠正 SEDAR 的 comment-before-mutation and restore-after-mutation 描述，并准确解释 MORPH 与 SEDAR 的方法差异**。不是整篇 Major Revision 的完成报告。在修改稿中，应着重对该研究的技术贡献及其与既有工作之间的关系，给出更准确且论据充分的阐述。

| 文件 | 用途 | 处理原则 |
| --- | --- | --- |
| `../MajorRevisonBack.tex` | 原始提交版本、最终 diff 基准 | 不修改，包括文件名中的拼写 |
| `../main.tex` | clean 正文 | 所有正式论文修改首先落实到这里 |
| `../responseletter.tex` | 回复信 | 按正文已经完成的修改写回复 |
| `MajorRevision.md` | 评审意见与已有 rebuttal | 作为修改要求和已有实验陈述的来源 |
| 本文件 | 修改计划 | 中文对照、核实清单 |


## 二、建议采用的工作顺序

1. 对照评审原文确定每条要求，保留评审顺序和原文，不擅自缩短或重新组合问题。
2. 确认回复所依赖的机制、代码、实验或原始记录；已有 rebuttal 数字可用于草稿，但必须核验后定稿。
3. 修改 `main.tex`，同步修改图、图注、方法描述和实验解释中相互冲突的地方。
4. 根据实际完成的修改写 `responseletter.tex`，说明修改位置、内容和必要的原句对照。不能仅把 “We will” 换成 “We have” 就视为已经落实。
5. 对相同问题在其他 reviewer 处简短回答并交叉引用；包含多个问题的长评审意见不能仅用一个交叉引用替代全部回答。
6. 每条完成后核对 clean 与回复信；可随时查看 Git diff。最终统一用 `MajorRevisonBack.tex` 和 clean 生成提交用高亮 diff，不手工维护 diff 为第二份正文。

## 三、本条修改的核心立场

- SEDAR 的临时注释发生在变异前，恢复发生在变异后、目标 DBMS 执行前。不能把中间的隐藏形式当成最终执行查询。
- MORPH 的差异需要落到 AQT 实例化、目标端上下文、解析/执行反馈和变异前修复等具体环节，而不是建立在 SEDAR 永久删除查询逻辑的描述上。
- 动机案例只能支持该案例中实际观察到的失败；不能将一次错误推广为 SEDAR 恢复机制必然失败。
- 完整流程的覆盖率、漏洞数和特征计数，不能单独证明某个处理环节造成了差异。
- MORPH 的常量替换和最小范围注释也可能改变行为。两种策略均不保证变异后的语义等价性。
- 最终正文应减少重复辩护：机制集中讲清楚，局限在方法和相关实验处说明，不必每一节都重复同一句免责声明。

## 四、逐项修改计划及中文前后对照

以下按 `main.tex` 源码顺序列出。源码注释不出现在 PDF 中，但也纳入对照。英文原句和当前替换句见文末完整差异附录，可据此精确定位。

### 2. Introduction：SEDAR 介绍与两项挑战

**修改前：** 最近面向模糊测试的方法利用 LLM 进行端到端测试用例迁移。例如，SEDAR 把模式信息和原始 SQL 文本输入 LLM，生成目标方言种子。但这类方法有两个根本局限：第一，对目标方言理解有限、对多语句结构感知较弱，可能引入语法或语义错误，导致无效或不一致的查询行为；第二，为满足下游模糊测试器严格的语法约束，通常删除或注释不支持的结构进行清理，可能简化 SQL 结构并削弱执行行为。

**修改后第一段：** 最近面向模糊测试的方法利用 LLM 在 DBMS 之间迁移已有测试用例。SEDAR 把源端模式信息和 SQL 语句输入 LLM，按原始顺序重新组装翻译后的语句。其可变异性优化会暂时注释下游模糊测试器不能解析的片段，对剩余部分进行变异，再在目标 DBMS 执行变异 SQL 前恢复片段。被隐藏的片段会保留用于执行，并非永久删除。

**修改后第二段：** 两个挑战推动我们的设计：翻译引入的错误可能使迁移后的测试无法执行；被解析器隐藏的结构在隐藏期间无法参与其基于语法的变异，而且恢复本身不修复翻译错误。因此，需要在下游变异之前进行反馈引导的修复。

**理由与计划：** 将“不完整描述导致的逻辑丢失”改成真实处理流程和修复时机差异。当前这一段引用从 SEDAR、QTRAN 改为只引用 SEDAR；RC2 修改时应检查 QTRAN 在其他位置的讨论是否充分，不把不同方法笼统归为同一机制。“隐藏期间不能参与语法变异”的适用范围应与实际 fuzzer 的操作方式对应。

### 3. Introduction：删除旧方法总结注释

**修改前（注释）：** 这使模糊测试能够保留与测试有关的 SQL 特征，同时避免直接文本翻译引起的翻译失败和破坏性清理。

**修改后：** 删除。

**理由：** 不保留仍沿用“破坏性清理”对比的备用文字。

### 4. Background 写作指导：删除旧失败链注释

**修改前（注释）：** 动机示例必须写成失败过程：SEDAR 幻觉 → 注释式清理 → 逻辑丢失。

**修改后：** 删除。

**理由：** 该链条遗漏恢复步骤。新案例必须基于可追踪的实际输入、中间状态、恢复后查询和执行结果。

### 5. Cross-Dialect Seed Transfer：SEDAR 与 MORPH 的区别

**修改前：** SEDAR 等工作探索基于 LLM 的种子迁移。虽然翻译完整测试用例提升了自动化程度，但迁移中仍没有显式保留跨语句依赖。

**修改后：** SEDAR 利用源端模式信息迁移完整测试用例，通过变异前临时注释和变异后恢复解决下游解析器不兼容。MORPH 利用不断演化的目标端上下文协调实例化，并在下游变异前修复迁移后的不兼容结构。

**理由与计划：** 用具体机制替代对 SEDAR 缺乏依赖处理的笼统断言。这也涉及 RC2 创新性边界，后续应与相关工作及能力表一起核对，不能只靠“源端/目标端”措辞证明全部创新。

### 6. Cross-Dialect Seed Transfer 后：删除单说注释的旧注释

**修改前（注释）：** 为提升 fuzzer 兼容性，它采用不可解析片段注释机制，通过注释不兼容的部分或语句处理解析错误。

**修改后：** 删除。

**理由：** 该备用句没有说明恢复，正式正文已经使用完整描述。

### 7. Motivating Example：删除两个备用开头注释

**修改前注释一：** 下文用一个具体例子说明 SEDAR 在跨方言种子迁移中的局限。

**修改前注释二：** 我们使用最接近的现有面向模糊测试种子迁移系统 SEDAR 来说明这一差距。

**修改后：** 两条均删除。

**理由：** 重写开头时移除重复备用表述；这两句话本身并非机制性错误。

### 8. Motivating Example：开头正文

**修改前：** 接下来用动机示例说明 SEDAR 等基于 LLM 的迁移方法的局限。此类方法有助于生成初始种子，但可能因幻觉引入语法或语义错误。许多 fuzzer 只支持部分 SQL 语法。现有方法通常注释不支持的特征以支持迁移后的变异，这会改变查询并导致结构简化。

**修改后：** 图 1 对比一个 PostgreSQL 到 DuckDB 的测试用例中的兼容性处理流程。测试包含使用 AGE 和 ::INTERVAL 转换的时间谓词。区别在于变异期间临时隐藏片段，或变异前修复片段；被隐藏后的中间形式并不是 SEDAR 最终送给 DBMS 的输入。

**理由与计划：** 区分中间表示与最终执行查询。最终需要让图中真实 SQL 与 AGE、INTERVAL 的正文描述一一对应。

### 9. 动机图本体：当前草稿替换与后续重画计划

**修改前：** 使用 `media/motivating_71.pdf`，源码通过 includegraphics 插入。原图以具体 SQL 转换说明语义错误及结构简化。

**当前修改后：** 不再引用该 PDF，改用两个 LaTeX 流程框。原 PDF 文件保留，避免影响冻结版本。保留 `fig:motivation` 标签；两框各宽约 0.43 栏宽，较小字号、左对齐。

**新图全部文字的中文翻译：**

| 位置 | 文字 |
| --- | --- |
| 顶部 | 源测试：时间谓词和区间类型转换 |
| 左标题 | SEDAR：隐藏与恢复 |
| 左步骤 1 | 利用源端模式进行翻译 |
| 左步骤 2 | 暂时注释解析器不兼容的片段 |
| 左步骤 3 | 对可解析部分进行变异 |
| 左步骤 4 | 恢复被隐藏的片段 |
| 左步骤 5 | 在目标 DBMS 上执行 |
| 左结语 | 仅恢复本身不能修复翻译错误 |
| 右标题 | MORPH：变异前修复 |
| 右步骤 1 | 将测试抽象为 AQT |
| 右步骤 2 | 在目标方言和上下文约束下实例化 |
| 右步骤 3 | 利用解析/执行反馈修复不兼容 |
| 右步骤 4 | 标记可变异区域 |
| 右步骤 5 | 变异并执行 |
| 右结语 | 修复后的种子传递给下游 fuzzer |

**尚未定稿：** 目前是流程对比图，减少了原图的具体 SQL 细节。建议最终保留具体 SQL 案例：源 SQL → 翻译结果 → 临时注释形式 → 变异 → 恢复后的 SQL → 目标执行结果；MORPH 一侧展示对应抽象、修复和交给 fuzzer 的 SQL。只有取得原始记录后才填写具体恢复内容和错误原因。不要将 WHERE TRUE 的中间形式当作最终执行结果。新图应另存素材，不覆盖冻结原稿引用的旧图。

### 10. 动机图：正式图注与备用图注

**修改前备用注释：** 动机示例：SEDAR 导致语义偏离和逻辑丢失，而 MORPH 通过 AQT 保留意图。

**修改后备用注释：** 删除。

**修改前正式图注：** SEDAR 等传统端到端翻译方法存在语义错误和结构简化问题（左）；MORPH 通过 AQT 解耦意图，成功实例化等价的 date_diff 逻辑（右）。

**修改后正式图注：** 动机迁移案例中的兼容性处理。SEDAR 在变异后、目标执行前恢复隐藏片段；MORPH 在下游变异前修复迁移种子。两种策略都不保证变异后的语义等价。

**计划：** 最终图注跟随实际重画的案例图修改，不把未验证的 date_diff 等价性写成保证；是否在图注重复等价性局限可在精简时决定。

### 11. 现有方法局限：改为观察到的失败范围

**修改前：** 图 1 展示 SEDAR 把含 AGE 和 ::INTERVAL 的 PostgreSQL 测试迁移到 DuckDB 时的语义偏离与逻辑丢失。LLM 因不了解 DuckDB 语法而产生幻觉、保留 AGE，导致语法和语义错误。SEDAR 为满足解析约束注释 AGE，将 WHERE 条件替换为 True，得到语法有效但简化的查询，丢失验证意图、无法触及深层状态。因此，这些种子对后续有效变异的作用有限。

**修改后：** 在实验报告的动机案例里，恢复步骤重新引入了存在问题的翻译后结构，最终查询在目标 DBMS 上产生语义错误。这是该案例的观察，并不能证明 SEDAR 永久删除查询逻辑或恢复机制必然失败。注释后的形式是用于变异的中间表示；必须区分恢复后的错误和临时隐藏这一步。

**理由与计划：** 依据 rebuttal 限定案例结论，删除未核实的具体因果推断。需要找到原始 trace，确认错误确实在所描述阶段出现；不能仅凭 AGE 名称断言目标不支持它。

### 12. 失败段后：新增三条核查注释

**修改前：** 无。

**修改后（逐条翻译）：**

1. RC1 的证据来源：MajorRevision/MajorRevision.md，Reviewer A，Comment 3。
2. 提交前，在实验材料中提供原始迁移、变异和恢复记录的链接。
3. 没有该记录时，不要推断具体错误原因或笼统断言不支持 AGE。

**计划：** 完成记录核实后更新待办注释，不用注释替代真正的证据归档。

### 13. Basic Idea of MORPH

**修改前：** MORPH 彻底解耦逻辑意图和语法表示，从不稳定文本翻译转为稳健结构递归替换。它不会进行错误语法翻译，而是将 AGE 抽象为通用 TimeDiff 意图，再高保真实例化为 DuckDB 的等价语法。通过保留类型转换和窗口函数分区逻辑，它生成保留过滤复杂度的高保真种子；维持复杂语义确保种子有效，并能到达更深路径、发现 SEDAR 可能无法触及的复杂逻辑错误。

**修改后：** MORPH 分离结构抽象和目标方言实例化。该例使用 TimeDiff 标签表示时间差操作，结合目标方言和类型信息实例化，再利用解析和执行反馈。目标是在保留有用结构的同时生成可执行、fuzzer 兼容的种子。与临时隐藏和恢复不同，它尝试在交给 fuzzer 前修复迁移结构；不保证变异后的语义等价。

**理由与计划：** 去掉“不会翻译错误”“确保等价”等保证；删除当前时间谓词案例没有解释的窗口分区内容。后续仍应保留清晰的设计动机，不能只剩局限声明；具体操作须与新图及实现一致。

### 14. 反馈驱动节：删除对比指导注释

**修改前（注释）：** 强调注释是最后兜底，与 SEDAR 式默认清理拉开距离。

**修改后：** 删除。

**理由：** 不用含糊的“默认清理”概括 SEDAR 的完整机制。

### 15. 反馈驱动节：变异前处理流程

**修改前：** 兼容种子经过清理，在目标 DBMS 中执行，必要时修复，并标记用于 fuzzing 的可变异区域。

**修改后：** 迁移种子先接受 fuzzer 解析器检查，在目标 DBMS 执行，必要时修复，并在下游变异前标记可变异区域。SEDAR 在变异期间隐藏解析器不兼容片段，之后恢复。区别在于不兼容问题的处理方式，两者都不保证变异后的语义等价。

**理由与计划：** 明确阶段顺序。核对实现中检查、执行、重试和标记的具体顺序，避免将迭代过程误写成永不回退的单向流程。

### 16. 反馈驱动节：删除旧英文备用段落注释

**修改前（注释）：** 当迁移种子 Q_target 输入 fuzzer 时，可能还有静态翻译未捕获的细微语法不兼容。清理器不丢弃种子，也不像 SEDAR 那样破坏性注释不可解析 token，而是捕获解析错误，定位对应 AQT 节点，利用语法反馈修复。例如，删除 date_diff 周围多余括号，生成语法有效的可变异种子。

**修改后：** 删除整个备用注释。相邻正式正文仍保留定位 AQT 节点、反馈修复、删除多余括号的说明。

**理由：** 清除注释中的错误对比，而非删除 MORPH 的修复步骤。

### 17. 四级兜底策略结尾

**修改前：** 因此，只在保持结构的修复失败后，才使用破坏性注释。

**修改后：** 常量替换和最小范围注释是兜底转换，可能改变测试行为；因此 MORPH 不声称每个修复种子都保留源测试语义。

**理由：** 回答我们自己的处理是否保证等价。四级顺序不变：已有映射 → RAG 候选 → 源端执行后常量替换 → 最小范围注释。

### 18. RQ2 漏洞数量结果的解释

**修改前：** 这证明覆盖率提升不仅具有统计意义，而且产生实际功能效果，能发现基线无法触及的漏洞。SEDAR 通过破坏性清理牺牲语义深度，而 MORPH 保留完整逻辑复杂度和可执行语义，从而绕过早期拒绝、深入代码并触发更多漏洞。

**修改后：** 结果支持迁移种子的实际效用，但比较的是完整流程，没有隔离可变异性优化的影响。SEDAR 会在变异后恢复隐藏片段，因此不能把漏洞数差异归因于片段被永久删除。

**理由与计划：** 不改变漏洞数字；删除实验不能单独支持的归因。“没有发现”不能直接写成“无法触及”。其他显著性措辞是否有统计依据，后续实验审查时一并处理。

### 19. RQ2 新增变异处理对照实验

**修改前：** 无该段。

**修改后：** 为在相同下游 fuzzer 下比较处理策略，采样相同的 300 个翻译后种子，用 GRIFFIN 应用两种策略运行 24 小时。MORPH 变异前修复在 DuckDB、MonetDB 上的分支覆盖率分别高于 SEDAR 注释恢复策略 27.2%、15.5%。比较限于所评估配置，不证明变异查询语义等价。

**来源：** 已有 rebuttal 的 Reviewer B Question 2。不是本轮新运行的实验，也不是 rebuttal 中另一个“100/200/300 个种子控制数量”的实验。

**必须核实后定稿：** 相同种子的具体含义和清单、目标 DBMS 对应关系、两种处理具体实现、版本、初始状态、资源限制、重复次数、覆盖统计口径、原始覆盖数及百分比算法。不能擅自补写“每个目标各 300 个”、随机重复次数或统计显著性。

### 20. 对照实验后的新增源码注释

**修改前：** 无。

**修改后（逐条翻译）：**

1. RC1 结果来源：MajorRevision/MajorRevision.md，Reviewer B，Question 2。
2. 提交前核对共用种子清单、运行次数、配置和原始覆盖记录。
3. 本次没有声称新运行或新增统计检验。

**计划：** 原始记录未找到之前，维持草稿状态；不能将“已在 rebuttal 写过”视为实验材料已经齐备。

### 21. RQ3：SEDAR 基线流程描述

**修改前：** 我们复现了 SEDAR 的完整流程，包括模式捕获、端到端 LLM 翻译以及注释式清理策略。

**修改后：** 所评估的 SEDAR 流程包括源端模式捕获、LLM 翻译以及变异前注释、变异后恢复的流程；对 fuzzer 解析器隐藏的片段在变异 SQL 送入目标 DBMS 前恢复。

**理由与计划：** 补全恢复机制；不在此未经核对地继续使用“复现”或“原始公开 binary”等来源描述。RC4 必须明确实际使用的是哪份实现、从哪里获得、做了哪些修改，当前改写不代表 RC4 已解决。

### 22. RQ3：删除特征多样性的旧 TODO 注释

**修改前（注释）：** 多样性差距主要来自清理策略：SEDAR 注释不能解析的转换或函数调用，而 MORPH 将抽象结构实例化为目标兼容形式，为下游变异保留更丰富特征。

**修改后：** 删除。

**理由：** 不再把临时注释当作特征永久丢失的证据。

### 23. RQ3：特征计数差距的解释

**修改前：** 多样性差距主要来自无法映射结构的处理：语句级翻译器报错或省略，SEDAR 注释，而 MORPH 用检索方言证据实例化为目标兼容形式，从而保留更丰富的变异输入。

**修改后：** 特征计数描述完整流程产生的有效迁移输出，不能隔离兼容性处理的影响。SEDAR 会在变异后恢复暂时注释的片段，因此临时注释本身不能证明 SQL 特征永久丢失。MORPH 使用方言引导实例化和反馈修复，为后续变异生成目标兼容结构。

**理由与计划：** 保留已有函数、类型和关键字数字，收敛解释范围。应核查特征计数究竟在恢复前还是恢复后、如何定义“有效输出”，再确认这段描述与统计脚本一致。

### 24. Related Work：LLM-Based Seed Generation 结尾两段

**修改前第一段：** SEDAR 与 MORPH 最接近，因为它也将已有 DBMS 测试迁移为 fuzzing 种子。但这些方法主要依赖直接文本生成或生成后的过滤和清理。

**修改前第二段：** MORPH 执行 AQT 引导的完整测试用例实例化，利用显式模式依赖、嵌套结构和子句关系，在保留上下文时支持局部修复。

**修改后第一段：** SEDAR 仍是最接近的工作。它提取源端模式用于翻译，变异前临时注释 fuzzer 解析器不兼容片段，目标执行前恢复，因此片段保留在实际执行查询中，并非永久删除。

**修改后第二段：** MORPH 结合不断演化的目标上下文执行 AQT 实例化，在下游变异前使用解析和执行反馈修复。区别在于协调翻译修复与为 fuzzer 兼容而临时隐藏恢复，而非 SEDAR 必然牺牲查询逻辑。两种策略不保证变异后的语义等价。

**理由与计划：** 与前文统一机制。RC2 时进一步阐明 AQT、上下文和局部修复的具体贡献，避免将 SEDAR 的全部方法概括为仅仅隐藏恢复。末尾等价性声明可视全篇重复程度精简。

## 五、回复信对应计划

| 回复位置 | 当前草稿 | 定稿要求 |
| --- | --- | --- |
| Requested Change 1 | 感谢后分三点：机制纠正、动机案例范围、实际方法差异及对照实验 | 按实际定稿正文核对节号、图号、旧句新句及实验记录 |
| Comment 2.3 | 简要确认纠正，引用 RC1 | 保持简短，避免重复整段 |
| Comment 2.8 | 说明描述修订和共同 fuzzer 对照，引用 RC1 | 实验配置和结果核实后才能视为完成 |
| Comment 2.14 | 直接回答两者均不保证变异后等价，解释差异，引用 RC1 | 不拿迁移测试的等价率充当变异后等价率 |
| Comment 1.8 | 仍为 TODO | 该条还有其他评估/复现问题，不能整体用 RC1 引用替代 |
| Requested Change 4 | 仍为 TODO | 单独核实 SEDAR 实现来源，不在 RC1 中假称已完成 |

采用师兄的回复写法：先给明确回应，再说明在哪一节改了什么；需要时引用原句与新句；实验补充给出设置和结果；重复问题用交叉引用。评审意见原文及原始小标题不改，统一 Comment R.k 编号不改变评审出现顺序。

## 六、后续执行与验收清单

- [x] 冻结原稿保持不变，核对 SHA256。
- [x] clean 中补全 SEDAR 注释、变异、恢复、执行顺序。
- [x] 清理本轮涉及的错误正文和备用注释。
- [x] 加入来自 rebuttal 的对照实验草稿，并标明核实任务。
- [x] 写入 RC1 及三个直接相关 B 评论的回复草稿。
- [ ] 找到动机案例原始 SQL、中间状态、恢复结果和执行错误记录。
- [ ] 将临时流程图完善为具体 SQL 案例图，保留正确的恢复步骤。
- [ ] 核实 300 种子对照实验的配置、种子清单和覆盖原始值。
- [ ] 核实特征计数的采样阶段和统计口径。
- [ ] 在 RC4 中澄清实际 SEDAR 实现来源和修改情况。
- [ ] 精简重复的“不是永久删除”“不保证等价”措辞，保留主要论证。
- [ ] 与 RC2 的创新性、能力表和相关工作修改统一。
- [ ] 检查正文全部引用、图表布局、页数限制及回复信节号图号。
- [ ] 所有实际修改落实后，逐句核对回复中的 “We have” 陈述。
- [ ] 最终从冻结原稿生成高亮 diff，并确认生成物不覆盖 clean 或原稿。

## 七、英文原文与修改后源码完整对照

以下附录是撰写本计划时 `git diff -- main.tex` 的快照。`-` 表示修改前，`+` 表示当前草稿；保留了英文原文、LaTeX、源码注释及上下文。它用于精确核对本轮改动，不替代最终提交时与冻结原稿生成的 diff。

```diff
diff --git a/main.tex b/main.tex
index b295acc..a9a9b49 100644
--- a/main.tex
+++ b/main.tex
@@ -158,7 +158,6 @@ DBMS Fuzzing, Initial Seeds, Cross-DBMS SQL Transfer
 \end{IEEEkeywords}
 
 % Reviewer 修改逻辑总览：
-% - Reviewer B/Q1 novelty vs. SedaR：Introduction、Approach overview、Related Work 统一强调 SedaR 是 LLM text translation + destructive sanitization，Morph 是 AQT-Guided whole-test-case instantiation + feedback repair，并用 RQ3/RQ2 数字支撑差异。
 % - Reviewer A/Q1 novelty vs. RISE：Introduction、Related Work、RQ3 统一强调 RISE 是 SELECT statement-level translation，Morph 是 fuzzing seed 的 whole-test-case transfer；rules 只是局部 AQT-node instantiation operator。
 % - Reviewer C/Q1 semantic correctness：Evaluation setup/RQ3 明确 semantic correctness 指目标 DBMS 无 runtime error 执行成功，区别于源/目标结果等价；semantic richness 另用 functions/data types/keywords 衡量。
 % - Reviewer B/C extensibility/manual effort：Discussion 中用 manual effort 表说明规则主要自动抽取，人工只 review/correction 少量 unmapped constructs。
@@ -195,9 +194,8 @@ While established DBMSs (e.g., \pg{}~\cite{pg_git}) possess extensive test suite
 To construct initial seeds for these DBMSs, a common practice is to reuse test cases from other mature systems, but SQL dialect incompatibilities often hinder this process.
 Existing SQL translation techniques~\cite{sqlglot,jooq,xie2026rise} focus on statement-level translation, which treats SQL statements independently. In contrast, DBMS fuzzing seeds are multi-statement test cases with rich cross-statement dependencies (e.g., schema references or type constraints). As a result, statement-level translation may break dependency chains, leading to execution failures when transferred across systems.
 
-Recent fuzzing-oriented approaches instead leverage large language models (LLMs) for end-to-end test-case transfer~\cite{fu2024sedar, lin2025qtran}. For example, \sedar{}~\cite{fu2024sedar} combines schema information and raw SQL text as input to an LLM to generate target-dialect seeds. However, such approaches still suffer from two fundamental limitations: 
-(i) First, due to limited understanding of target SQL dialects and weak structural awareness of multi-statement programs, LLM-based translation may introduce syntactic or semantic errors during generation, leading to invalid or inconsistent query behaviors.
-(ii) Second, to satisfy strict grammar constraints imposed by downstream fuzzers, generated test cases are often sanitized by removing or commenting out unsupported constructs, which may simplify complex SQL structures and weaken execution behaviors.
+Recent fuzzing-oriented approaches leverage large language models (LLMs) to transfer existing test cases across DBMSs~\cite{fu2024sedar}. For example, \sedar{} combines source-schema information and SQL statements as input to an LLM and reassembles the translated statements in their original order. Its mutability refinement temporarily comments out fragments that the downstream fuzzer cannot parse, mutates the remaining parts, and restores the commented-out fragments before executing the mutated SQL on the target DBMS. This mechanism retains the hidden fragments for execution rather than permanently removing them.
+Two challenges motivate our design. First, errors introduced during translation can prevent a transferred test case from executing. Second, constructs hidden from the fuzzer's parser are unavailable to its syntax-based mutation while hidden, and restoring them does not itself repair translation errors. These challenges motivate feedback-guided repair of transferred seeds before downstream mutation.
 
 % (i)  syntactic or semantic errors due to limited understanding of target SQL dialects, (ii) structural simplification, where complex multi-statement interactions are reduced or flattened, resulting in less diverse execution behaviors, and (iii)
 % transferred seeds may violate the strict grammar subset supported by downstream fuzzers.
@@ -251,7 +249,6 @@ However, realizing this idea has three challenges:
 % To realize this insight, \tool{} first extracts an AQT that explicitly captures schema dependencies, nested structures, and clause-level relationships.
 % It then instantiates the AQT into target-dialect SQL using local AQT-node mappings and target-side feedback, with the LLM used only for bounded rule generation or local repair under structural constraints.
 % Unlike statement-level translators such as RISE~\cite{xie2026rise}, which reduce an individual SQL statement to a statement-local rewrite rule, \tool{} abstracts a whole test case into an AQT that explicitly records cross-statement read/write dependencies, an evolving schema context, and nested clause-level structure; mapping rules are only local operators for instantiating AQT nodes, rather than the core translation paradigm.
-% This enables fuzzing to retain testing-relevant SQL features while avoiding the translation failures and destructive sanitization caused by direct text translation.
 
 % Paragraph 7 写作逻辑：
 % - 方法段按照 Challenge 1/2/3 的顺序写三个组件。
@@ -304,7 +301,6 @@ Compared to the state-of-the-art method \sedar{}, \tool{} improves the overall s
 % 回应逻辑：这里强调目标 DBMS 可执行、结构和 feature richness 能保留下来，后面 RQ3 用 validity 与 F/DT/K 两组指标分开验证。
 % - 先讲 SQL dialect diversity 为什么让 test assets 难以复用。
 % - 再讲 DBMS fuzzing 依赖 initial seeds，seed scarcity 推动 cross-dialect transfer。
-% - Motivating example 必须写成失败过程：SedaR hallucination -> comment-out sanitization -> logic loss。
 % - Basic Idea  AQT：把 logical intent 从 dialect-specific syntax 中抽离，再实例化到目标 DBMS。
 \textbf{SQL Dialects.} 
 \textit{Database Management Systems (DBMSs)} underpin modern data-driven applications and expose high-level declarative query languages for data interaction. Although SQL is standardized, real-world DBMSs implement diverse \textit{SQL dialects} with substantial syntactic and semantic variations.
@@ -352,7 +348,7 @@ However, while mature DBMSs such as PostgreSQL provide rich test suites, many em
 \textbf{Cross-Dialect Seed Transfer.} To address seed scarcity for emerging DBMS testing, cross-DBMS SQL transfer has emerged as an active research direction~\cite{zhou2020database}, but this is challenging due to SQL dialect differences.
 As shown in Table~\ref{tab:translation-capability}, most existing tools do not explicitly preserve cross-statement dependencies, making them unsuitable for test-case-level transfer in fuzzing.
 Specifically, existing SQL translation techniques~\cite{sqlglot, jooq, xie2026rise} focus on statement-level translation, which treats each SQL statement independently. In contrast, fuzzing seeds are multi-statement test cases that include complex interactions among DDL, DML, and DQL statements.
-Recent work such as \sedar{}~\cite{fu2024sedar} explores fuzzing-oriented seed transfer using LLMs. While it improves automation by translating complete test cases, it still does not explicitly preserve cross-statement dependencies during migration.
+\sedar{}~\cite{fu2024sedar} transfers complete test cases using source-side schema information. It addresses downstream parser incompatibilities through temporary commenting before mutation and restoration afterward. \tool{} instead coordinates instantiation with evolving target-side context and repairs incompatible transferred constructs before downstream mutation.
 
 
 
@@ -361,7 +357,6 @@ Recent work such as \sedar{}~\cite{fu2024sedar} explores fuzzing-oriented seed t
 
 
 
-% To improve fuzzer compatibility, it applies unparsable commenting to handle parsing errors by commenting out incompatible parts or statements.
 % The efficacy of fuzzing is heavily influenced by the quality of \textit{Initial Seeds}. 
 % Due to the diverse features of DBMSs, fuzzers lacking high-quality initial seeds with specific syntactic features (e.g., recursive CTEs) are often confined to shallow parsing logic, thus leaving many bugs undetected~\cite{wang2021industry}.
 
@@ -379,22 +374,36 @@ Recent work such as \sedar{}~\cite{fu2024sedar} explores fuzzing-oriented seed t
 
 
 \textbf{Motivating Example.}
-% In the following, we illustrate with a concrete example the limitations of \sedar{} in cross-dialect seed transfer.
-% We illustrate this gap using \sedar{}, the closest existing fuzzing-oriented seed-transfer system.
-We next present a motivating example to illustrate the limitations of LLM-based fuzzing-oriented seed transfer methods such as \sedar{}.
-LLM-based cross-dialect transfer methods help in the generation of initial fuzzer seeds but may introduce \textit{syntax or semantic errors} due to hallucinations.
-Moreover, many fuzzers cover only a subset of SQL grammar.
-Existing methods typically comment out unsupported features to support seed mutation after transfer, which alters the query and causes \textit{structural simplification}.
+Figure~\ref{fig:motivation} contrasts the compatibility-handling workflows for a PostgreSQL-to-DuckDB test containing a temporal predicate with \texttt{AGE} and an \texttt{::INTERVAL} cast. The distinction is between temporarily hiding a fragment during mutation and repairing it before mutation; a hidden intermediate form is not \sedar{}'s final input to the DBMS.
 
 \begin{figure}[htbp]
   \centering
-  \includegraphics[width=\linewidth]{media/motivating_71.pdf}
-  % \caption{Motivating example: \sedar{} causes semantic deviation and logic loss, while \tool{} preserves intent via AQT.}
-  \caption{Conventional end-to-end translation methods like \sedar{} suffer from semantic error and structural simplification (Left). \tool{} decouples intent via AQT, successfully instantiating the equivalent \texttt{date\_diff} logic (Right).}
+  {\footnotesize\textbf{Source test: temporal predicate and interval cast}\par}
+  \smallskip
+  \fbox{\begin{minipage}[t]{0.43\columnwidth}
+    \footnotesize\raggedright
+    \textbf{SEDAR: hide and restore}\strut\par\smallskip
+    1. Translate with source schema.\par
+    2. Temporarily comment out parser-incompatible fragments.\par
+    3. Mutate the parsable parts.\par
+    4. Restore hidden fragments.\par
+    5. Execute on the target DBMS.\par\smallskip
+    Restoration alone does not repair a translation error.
+  \end{minipage}}\hfill
+  \fbox{\begin{minipage}[t]{0.43\columnwidth}
+    \footnotesize\raggedright
+    \textbf{MORPH: repair before mutation}\strut\par\smallskip
+    1. Abstract the test as an AQT.\par
+    2. Instantiate under target dialect and context constraints.\par
+    3. Apply parser/execution feedback to repair incompatibilities.\par
+    4. Mark mutable regions.\par
+    5. Mutate and execute.\par\smallskip
+    Repaired seeds are passed to the downstream fuzzer.
+  \end{minipage}}
+  \caption{Compatibility handling in the motivating transfer case. \sedar{} restores hidden fragments after mutation and before target-side execution; \tool{} repairs transferred seeds before downstream mutation. Neither strategy guarantees semantic equivalence after mutation.}
   \label{fig:motivation}
 \end{figure}
 
-
 \begin{figure*}[t]
   \centering
   \includegraphics[width=\linewidth]{media/overview-ase-new.pdf}
@@ -411,14 +420,13 @@ Existing methods typically comment out unsupported features to support seed muta
   \label{fig:workflow}
 \end{figure*}
 
-\textit{Limitations of Existing Approaches.} Figure \ref{fig:motivation} illustrates the semantic deviation and logic loss \sedar{} experiences when transferring a test case from \pg{} to \duckdb{}, which contains dialect-specific features like the \texttt{AGE} function in the \texttt{WHERE} clause and the \texttt{::INTERVAL} type cast. With limited knowledge of \duckdb{}’s syntax, the LLM hallucinates and preserves \texttt{AGE}, causing syntax and semantic errors. To satisfy parsing constraints, \sedar{} comments out \texttt{AGE} and replaces the \texttt{WHERE} condition with \texttt{True}, producing a syntactically valid but simplified query that loses the original verification intent and fails to exercise deep internal states. 
-As a result, the migrated seed has limited utility for subsequent effective mutation in DBMS fuzzing.
+\textit{Scope of the Observed Failure.} In the motivating case reported in our experiments, restoration reintroduced a problematic translated construct and the resulting query produced a semantic error on the target DBMS. This is an observation from that case, not evidence that \sedar{} permanently deletes query logic or that its restoration mechanism necessarily fails. The commented form is an intermediate representation for mutation. The error after restoration must be distinguished from this temporary hiding step.
+% RC1 evidence source: MajorRevision/MajorRevision.md, Reviewer A, Comment 3.
+% Before submission, link the original transfer/mutation/restoration trace in the artifact.
+% Do not infer a specific error cause or general lack of AGE support without that trace.
 
 \textit{Basic Idea of \tool{}.}
-The core idea of \tool{} is to thoroughly decouple logical intent from syntactic representation, shifting from unstable textual translation to more robust structural recursive substitution. 
-As shown in Figure~\ref{fig:motivation}, \tool{} does not perform erroneous syntactic translation, but instead abstracts the dialect-specific \texttt{AGE} function into a generic $<$\texttt{TimeDiff}$>$ intent. 
-It then performs a high-fidelity instantiation into \duckdb{}’s equivalent syntax. By ensuring the type casting and window function partition logic are preserved, \tool{} generates a high-fidelity seed that retains the original filtering complexity. 
-By maintaining these complex semantics during transfer, \tool{} ensures the generated seeds are effective for validation, enabling them to reach deeper code paths within the DBMS and uncover intricate logic bugs that might be unreachable by \sedar{}.
+\tool{} separates structural abstraction from target-dialect instantiation. For this example, it represents the time-difference operation using a \texttt{TimeDiff} label and instantiates it with target-side dialect and type information, followed by parser and execution feedback. The aim is to produce executable, fuzzer-compatible seeds while retaining useful structures. Unlike temporary hiding and restoration, this approach attempts to repair the transferred construct before it is passed to the fuzzer; it does not guarantee semantic equivalence after mutation.
 \section{Approach}
 \label{sec:approach}
 % Approach 写作逻辑：
@@ -654,7 +662,6 @@ As shown in Figure~\ref{fig:agent_translation}, since \texttt{ID1} depends on th
 % - 目标：把“目标 DBMS 能执行”的 seed 进一步变成 fuzzer parser 能接受、能 mutation 的 seed。
 % - 挑战：fuzzer 支持的 grammar 通常小于 DBMS，直接 comment out 会破坏结构和测试价值。
 % - 四级 fallback 顺序必须清楚：已有 mapping -> RAG candidate -> source DBMS 执行后常量替换 -> 最小 comment out。
-% - 强调 comment out 是最后兜底，和 SedaR 式默认 sanitization 拉开距离。
 \subsection{Feedback-Driven Adaptive Fuzzing}
 \label{sec:mutation_loop}
 % 段落写作逻辑：
@@ -671,7 +678,7 @@ The challenge is to adapt such features into fuzzer-compatible forms while prese
 % \noindent
 % \textbf{Feedback-Guided Adaptation.}
 \tool{} addresses this challenge using parsing and execution feedback to repair unsupported constructs through a structure-preserving fallback process.
-As shown in Figure~\ref{fig:feedback-driven}, compatible seeds are sanitized, executed in the target DBMS, repaired when necessary, and annotated with mutable regions for fuzzing.
+As shown in Figure~\ref{fig:feedback-driven}, transferred seeds are checked against the fuzzer parser, executed in the target DBMS, repaired when necessary, and annotated with mutable regions before downstream mutation. \sedar{} instead temporarily hides parser-incompatible fragments during mutation and restores them afterward. This difference concerns how incompatibilities are handled; neither strategy guarantees semantic equivalence after mutation.
 
 \begin{figure}[htbp]
   \centering
@@ -684,7 +691,6 @@ As shown in Figure~\ref{fig:feedback-driven}, compatible seeds are sanitized, ex
 % \smallskip
 % \noindent
 % \textbf{Error-Guided Sanitization.}
-% Specifically, when a transferred seed $Q_{target}$ is fed into the fuzzer, it may still contain subtle syntax incompatibilities not captured by the static translation phase. Instead of discarding these seeds or destructively commenting out unparsable tokens (as in \sedar{}~\cite{fu2024sedar}), the sanitizer captures the parser error message, localizes the unsupported dialect-specific construct to the corresponding AQT node, and repairs it using syntactic feedback. For instance, in Figure~\ref{fig:feedback-driven}, it automatically removes excess brackets around the \texttt{date\_diff} function to produce a syntactically valid Mutable Seed.
 Specifically, when a transferred seed $Q_{target}$ still violates the fuzzer grammar, the sanitizer localizes the unsupported construct to its AQT node and repairs it using parser feedback rather than discarding it.
 For example, it removes excess brackets around \texttt{date\_diff} in Figure~\ref{fig:feedback-driven}, producing a syntactically valid Mutable Seed.
 
@@ -694,7 +700,7 @@ For example, it removes excess brackets around \texttt{date\_diff} in Figure~\re
 % \textbf{Tiered Fallback Strategy.}
 % When an error signal is received (e.g., ``Syntax error near SELECT''), \tool{} applies four fallbacks in order. First, it checks the sanitization dictionary and applies an existing mapping when the unsupported construct has a known target-compatible form. Second, if no exact mapping exists, it uses RAG to retrieve candidate replacements from dialect evidence and validates the rewritten fragment with the fuzzer parser. Third, if no suitable replacement can be found, \tool{} executes the original source-side construct in the source DBMS and substitutes the returned value as a constant, preserving the surrounding query structure whenever possible. Finally, only if these structure-preserving attempts fail, \tool{} comments out the minimal offending construct. This ordering makes destructive commenting a last resort rather than the default sanitization strategy.
 When an error signal is received (e.g., ``Syntax error near SELECT''), \tool{} applies four fallbacks in order: existing sanitization mappings, RAG-retrieved replacements, source-side execution followed by constant substitution, and finally minimal commenting.
-Thus, destructive commenting is used only after structure-preserving repairs fail.
+Constant substitution and minimal commenting are fallback transformations and may change test behavior; \tool{} therefore does not claim that every repaired seed preserves the source test's semantics.
 % \smallskip
 % \noindent
 % \textbf{Mutable-Region Annotation.}
@@ -916,8 +922,13 @@ Figure~\ref{fig:coverage_over_time} further shows earlier coverage growth and co
 
 The coverage improvement also brings more detected bugs.
 As shown in Table~\ref{tab:bugs}, the campaign exposes significantly more unique bugs than the baselines, including 16 more bugs than \sedar{} across the four DBMSs. 
-This proves that coverage gains are not merely statistical but functionally impactful, enabling the detection of bugs that remain unreachable to baselines.
-Unlike \sedar{}, which sacrifices semantic depth through destructive sanitization, \tool{} employs structure-aware transfer to retain the full logical complexity of source test cases. By preserving executable semantics during transfer, \tool{} enables fuzzers to bypass early engine rejections, exercise deeper code logic, and trigger more bugs.
+These results support the practical utility of the transferred seeds. They compare the complete pipelines and do not isolate the effect of mutability refinement. In particular, \sedar{} restores its temporarily hidden fragments after mutation, so the bug-count difference should not be attributed to permanent removal of these fragments.
+
+\textbf{Controlled Comparison of Mutation Handling.}
+To compare the handling strategies under a shared downstream fuzzer, we sampled the same 300 translated seeds and applied the two strategies using \griffin{} for 24 hours. With \tool{}'s pre-mutation repair, branch coverage was 27.2\% higher on DuckDB and 15.5\% higher on MonetDB than with \sedar{}'s comment-and-restore handling. This comparison concerns the two handling strategies in the evaluated setting and does not establish semantic equivalence of the mutated queries.
+% RC1 results source: MajorRevision/MajorRevision.md, Reviewer B, Question 2.
+% Before submission, verify shared-seed manifests, run counts, configurations,
+% and raw coverage records. No new run or statistical test is claimed here.
 % optimizer and executor
 
 
@@ -939,7 +950,7 @@ For translation effectiveness, we compare \tool{} against six baselines, includi
 The No translation strategy directly executes the original test cases collected from source DBMSs without modification. 
 SQLGlot and jOOQ represent off-the-shelf SQL dialect translators, while CrackSQL and RISE represent recent SQL translation systems.
 \sedar{} represents the current state-of-the-art LLM-based transfer tool in constructing initial seeds in fuzzing. 
-We reproduced \sedar{}'s complete pipeline, including schema capture, end-to-end LLM translation, and its comment-out sanitization strategy.
+The evaluated \sedar{} pipeline includes source-schema capture, LLM-based translation, and its comment-before-mutation and restore-after-mutation workflow: fragments hidden from the fuzzer parser are restored before the mutated SQL is executed on the target DBMS.
 
 % We evaluate \tool{} against untranslated seeds, SQL dialect translators, recent SQL translation systems, and \sedar{} 
 
@@ -999,8 +1010,7 @@ These gains confirm that Dialect-Aware AQT Instantiation maps source intent into
 Beyond validity, we measure whether transferred seeds retain testing value by counting valid functions, data types, and keywords.
 As shown in Table~\ref{tab:diversity}, \tool{} preserves on average 126\% more functions, 60\% more data types, and 73\% more keywords than baselines.
 % In particular, \tool{} discovers 66\% more functions, 23\% more data types, and 57\% more keywords compared to the state-of-the-art tool \sedar{}.
-% \TODO{The diversity gap mainly comes from sanitization strategy: \sedar{} may comment out unparsable casts or function calls, whereas \tool{} constructively instantiates abstract structures into target-compliant forms, preserving more feature-rich inputs for downstream mutation.}
-The diversity gap mainly comes from how each baseline handles unmappable constructs: statement-level translators raise errors or omit them, while \sedar{} comments them out, whereas \tool{} constructively instantiates abstract structures into target-compliant forms via retrieved dialect evidence, preserving more feature-rich inputs for mutation.
+These feature counts characterize the valid transferred outputs of the complete pipelines. They do not isolate the effect of compatibility handling. Since \sedar{} restores temporarily commented fragments after mutation, temporary commenting alone does not establish permanent loss of SQL features. \tool{} uses dialect-guided instantiation and feedback repair to produce target-compatible structures for subsequent mutation.
 
 %特别的，\tool{}相比Sedar多发现了66\% functions, 23\% data types, and 57\% more keywords.
 % 1,036 functions, 180 data types, and 1,368 keywords, exceeding \sedar{} by 411, 34, and 496, respectively (66\%, 23\%, and 57\%).
@@ -1265,10 +1275,8 @@ Unlike these works, \tool{} focuses on the upstream \textit{seed acquisition} pr
 \subsection{LLM-Based Seed Generation}
 Recent work has explored LLMs for generating or enriching testing inputs.
 Fuzz4All~\cite{xia2024fuzz4all} uses LLMs as a general input generator, and ShQveL~\cite{zhong2025testing} synthesizes SQL fragments for database testing.
-\sedar{}~\cite{fu2024sedar} is the closest work to \tool{} because it also transfers existing DBMS test cases into fuzzing seeds.
-However, these methods largely rely on direct text generation or post-generation filtering and sanitization.
-
-In contrast, \tool{} performs AQT-guided whole-test-case instantiation, where explicit schema dependencies, nested structures, and clause-level relationships support local repair while preserving context.
+\sedar{}~\cite{fu2024sedar} is the closest work to \tool{} because it also transfers existing DBMS test cases into fuzzing seeds. It extracts source-side schema information for translation and temporarily comments out fuzzer-parser-incompatible fragments before mutation, restoring them before target-side execution. This preserves those fragments in the executed query rather than permanently deleting them.
+\tool{} performs AQT-guided instantiation with evolving target-side context and parser/execution-feedback repair before downstream mutation. The distinction is coordinated translation and repair versus temporary hiding and restoration for fuzzer compatibility, not that \sedar{} necessarily sacrifices query logic. Neither strategy guarantees semantic equivalence after mutation.
 
 
 % \subsection{Cross-Dialect SQL Translation}
```

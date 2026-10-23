# Requested Change 1 修改计划与逐段对照

更新时间：2026-10-23。

## 一、范围

只处理 RC1：准确说明 SEDAR 的变异前临时注释、变异后且目标执行前恢复，并纠正由临时隐藏推导出的永久结构/逻辑丢失结论。

- 核心条目：**18、23**。
- 等证据条目：**19**。

全文及图的既有审计记录已移至 [RC1_Audit_Appendix.md](RC1_Audit_Appendix.md)，不作为新增执行项。

## 二、执行方式

1. 每次只讨论并实施你认可的一项，未认可的条目保留。
2. 第四节保留中文说明，第七节保留英文原文与拟修改源码；等证据条目不先写入 clean。
3. 实施并核对后，删除第四节和第七节对应条目，其余编号不重排。
4. 仅实施本轮明确授权的条目，完成后清理对应计划记录。
5. RC1 以外的问题只在第五节记一行，不新增条目或展开处理。

## 三、修改原则

- 补全恢复流程，不把变异中的隐藏形式当作最终执行查询。
- 保留 AQT、前向读后写依赖、目标端上下文和反馈修复的原有主线，纠正具体错误比较。
- 保留正向实验结果，删除没有证据支持的永久逻辑丢失归因。
- 动机案例依据实际恢复/执行记录；常量替换和最小范围注释的行为变化仅在兜底策略结尾说明，变异后等价性在第 19 项正文明确一次。

## 四、RC1 条目及中文对照

### 18. RQ2 漏洞数量结果的解释

**修改前要点：** 漏洞数差异被归因于 SEDAR 通过破坏性清理牺牲语义深度，而 MORPH 保留完整逻辑并触及基线无法到达的路径。

**建议修改后：** 这些结果表明，MORPH 生成的迁移种子能够有效支持后续模糊测试，在所评估的运行中获得更高覆盖率并发现更多缺陷。其结构感知迁移结合目标端上下文和反馈修复，为下游 fuzzer 提供可执行且包含丰富 SQL 结构的种子。

**修改尺度：** 保留数字、结果比较和实际效用解释。去掉 SEDAR 永久删除逻辑的错误归因，以及“本次未发现”等同于“无法触及”的推断。单个兼容性处理环节的贡献由下一项实验讨论，无须在本段反复解释完整流程比较的限制。

### 19. RQ2：机制优势与处理策略比较（等证据）

**拟改内容：** 核实后用简短机制说明回答 claimed advantage，并在正文保留一次“不保证变异后查询语义等价”的说明；纳入已有 rebuttal 报告的 24 小时覆盖率比较（DuckDB +27.2%、MonetDB +15.5%）。

**所需证据：** 实际变异表示/算子及可变异区域标记；300 个种子的翻译来源、两端输入是否同一批 SQL，以及已有运行配置与原始覆盖结果。

**归因边界：** 只有同一批翻译后 SQL、其余配置一致且只改变处理策略，才能称 mutation-handling 对照。若分别翻译，则只在本项将比较名称和结论改为完整流程比较；事实未核实前暂不采用隔离机制的归因。

### 23. RQ3：特征计数差距的解释

**修改前要点：** 将多样性差距主要归因于 SEDAR 注释无法映射的结构。

**建议修改后：** 有效迁移输出中的函数、类型和关键字计数表明，MORPH 能够生成包含更丰富 SQL 特征的种子。其方言引导的 AQT 实例化与反馈修复旨在使复杂结构适配目标端和下游 fuzzer。

**修改尺度：** 保留特征数字及其反映的种子丰富度，删除错误的永久丢失归因。计数阶段和“有效输出”的定义按统计脚本核实，不在此重复完整流程无法隔离单个环节的说明。

## 五、待其他 RC

- **待 RC2：** Basic Idea 首句的 “thoroughly decouple” 和 “more robust structural recursive substitution” 等新颖性措辞，本轮保留。

- **待 RC4：** 核对 artifact 中 SEDAR 配置与生成种子的实际位置，并补足 binary 版本与获取信息；正文来源措辞已按作者说明更新。
- **待 RC2/其他意见：** 摘要概括（原第 25 项）。
- **待 RC2/其他意见：** RQ1 案例的 high-fidelity/路径不可达表述（原第 28 项）。
- **待 RC2：** QTRAN 引用及其余相关工作定位，另行处理。
- **待其他 RC：** 方法流程与概述的其余措辞（原第 15、27 项），暂不处理。
- **待其他 RC：** 纯源码注释整理，暂不处理。

## 六、当前待办

- [ ] Reviewer B Comment 4 (b)：为每个分类提供证据，留待回复信列出文档链接或实验验证。

- [ ] 机制描述与错误归因的剩余修改：18、23；每次仅实施一项。
- [ ] 19 取得所列证据后再定稿。

## 七、英文原文与修改后源码完整对照

以下只保留第四节同号条目。修改后均为拟稿，尚未应用。

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

### 7.19 RQ2：机制优势与对照实验（等证据）

**修改前：** 当前 clean 无对应新增段落。

**机制说明拟稿（等算子与标记证据）：**

```latex
\sedar{} restores hidden fragments for execution, but these fragments are excluded from parser-based mutation while hidden.
By repairing supported constructs into parser-compatible forms, \tool{} makes them available to the downstream mutator where permitted by its mutable-region annotations.
Neither strategy guarantees semantic equivalence of the mutated queries.
```

**实验拟稿（仅在共享翻译结果及控制配置均经核实时采用）：**

```latex
\textbf{Controlled Comparison of Mutation Handling.}
We applied the two handling strategies to an identical corpus of 300 translated seeds and ran each configuration with \griffin{} for 24 hours.
The translated input corpus, target database state, downstream fuzzer, and resource allocation were held fixed; only the handling strategy differed.
Branch coverage was 27.2\% higher on DuckDB and 15.5\% higher on MonetDB with \tool{}'s handling than with \sedar{}'s comment-and-restore handling.
```

若证据表明两端分别翻译，只在本项调整标题与归因，待事实明确后再给对应拟稿；不将该版本直接写入 clean。

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

# Requested Change 1 修改计划与逐段对照

更新时间：2026-10-23。

## 一、范围

只处理 RC1：准确说明 SEDAR 的变异前临时注释、变异后且目标执行前恢复，并纠正由临时隐藏推导出的永久结构/逻辑丢失结论。

- 核心条目：**13、17、18、23、24（只改 SEDAR 句）、26**。
- 第 **21** 项仅补全恢复步骤。
- 等证据条目：**8–11、19**。

全文及图的既有审计记录已移至 [RC1_Audit_Appendix.md](RC1_Audit_Appendix.md)，不作为新增执行项。

## 二、执行方式

1. 每次只讨论并实施你认可的一项，未认可的条目保留。
2. 第四节保留中文说明，第七节保留英文原文与拟修改源码；等证据条目不先写入 clean。
3. 实施并核对后，删除第四节和第七节对应条目，其余编号不重排。
4. 本轮仅清理已完成的计划记录，不修改 clean、回复信或图。
5. RC1 以外的问题只在第五节记一行，不新增条目或展开处理。

## 三、修改原则

- 补全恢复流程，不把变异中的隐藏形式当作最终执行查询。
- 保留 AQT、前向读后写依赖、目标端上下文和反馈修复的原有主线，纠正具体错误比较。
- 保留正向实验结果，删除没有证据支持的永久逻辑丢失归因。
- 动机案例依据实际恢复/执行记录；常量替换的行为变化在第 17 项说明，变异后等价性在第 19 项正文明确一次。

## 四、RC1 条目及中文对照

### 8. Motivating Example：开头正文（等证据）

**拟改内容：** 保留 PostgreSQL 到 DuckDB 的具体 SQL 案例，纠正“临时注释必然导致最终结构/逻辑丢失”的解释，补明执行前恢复。

**所需证据：** 源 SQL、翻译结果、隐藏/恢复后的 SQL 与实际执行反馈。证据齐全后再定稿，不先改 clean。

### 9. 动机图：保留具体 SQL，补全恢复步骤（等证据）

**拟改内容：** 沿用具体 SQL 对照布局，补上恢复阶段和真实结果；隐藏形式标为变异中间状态。保留 fig:motivation 标签，不换成泛化流程图。

**所需证据：** 实际恢复 SQL、执行反馈与 MORPH 对应输出。图素材和证据就绪后再单独确认图片引用；本项不包含图注。

### 10. 动机图：图注（等证据）

**拟改内容：** 图注只说明该例的翻译、临时注释、恢复、执行结果及 MORPH 的抽象/实例化/修复，去掉由临时隐藏推导结构永久简化的描述。

**所需证据：** 与第 9 项最终案例图一致；不把未经验证的等价性写成保证。

### 11. 动机案例：修正失败原因（等证据）

**拟改内容：** 删除“注释 AGE、WHERE TRUE 导致最终查询丢失逻辑”的因果链，依据记录说明恢复后是否仍因翻译不兼容而执行失败。

**所需证据：** 恢复后的 SQL、实际错误和原因。不能仅凭 AGE 名称断言目标不支持它；未取得记录前不应用拟稿。

### 13. Basic Idea of MORPH

**保留内容：** 结构抽象与目标方言实例化的核心思路、案例中的 TimeDiff 表示，以及保留有用测试结构的设计目标。

**局部调整：** 将“不会进行错误翻译”“确保有效”“必然到达更深路径”等保证性措辞改为设计目标或实测结果；仅在当前案例确实涉及时保留窗口分区说明。

**建议表述主线：** MORPH 将方言相关操作抽象为 AQT 节点，结合目标方言和类型信息实例化，并利用解析与执行反馈修复不兼容结构，生成保留有用测试结构的可执行种子。

**修改尺度：** 保留方法解释的具体性与正向动机，不将整段改为修复时机比较，也不追加变异后等价性声明。

### 17. 四级兜底策略结尾

**保留顺序：** 已有映射 → RAG 候选 → 源端执行后常量替换 → 最小范围注释。

**建议局部修改：** 只有前面的修复尝试失败后，才采用最小范围注释。常量替换和最小范围注释可能改变迁移种子的行为。

**修改尺度：** 保留“尽量先修复、最后兜底”的设计理由；在此说明一次迁移阶段的行为变化即可，不延伸为各节重复的免责声明。

### 18. RQ2 漏洞数量结果的解释

**修改前要点：** 漏洞数差异被归因于 SEDAR 通过破坏性清理牺牲语义深度，而 MORPH 保留完整逻辑并触及基线无法到达的路径。

**建议修改后：** 这些结果表明，MORPH 生成的迁移种子能够有效支持后续模糊测试，在所评估的运行中获得更高覆盖率并发现更多缺陷。其结构感知迁移结合目标端上下文和反馈修复，为下游 fuzzer 提供可执行且包含丰富 SQL 结构的种子。

**修改尺度：** 保留数字、结果比较和实际效用解释。去掉 SEDAR 永久删除逻辑的错误归因，以及“本次未发现”等同于“无法触及”的推断。单个兼容性处理环节的贡献由下一项实验讨论，无须在本段反复解释完整流程比较的限制。

### 19. RQ2：机制优势与处理策略比较（等证据）

**拟改内容：** 核实后用简短机制说明回答 claimed advantage，并在正文保留一次“不保证变异后查询语义等价”的说明；纳入已有 rebuttal 报告的 24 小时覆盖率比较（DuckDB +27.2%、MonetDB +15.5%）。

**所需证据：** 实际变异表示/算子及可变异区域标记；300 个种子的翻译来源、两端输入是否同一批 SQL，以及已有运行配置与原始覆盖结果。

**归因边界：** 只有同一批翻译后 SQL、其余配置一致且只改变处理策略，才能称 mutation-handling 对照。若分别翻译，则只在本项将比较名称和结论改为完整流程比较；事实未核实前暂不采用隔离机制的归因。

### 21. RQ3：只补全恢复步骤

**修改前：** SEDAR 流程被概括为模式捕获、LLM 翻译和注释式清理。

**拟改内容：** 仅将注释式清理补全为“变异前临时注释，变异后、目标 DBMS 执行前恢复”。句中其他表述保持不变；来源及措辞问题仅记入待 RC4 清单。

### 23. RQ3：特征计数差距的解释

**修改前要点：** 将多样性差距主要归因于 SEDAR 注释无法映射的结构。

**建议修改后：** 有效迁移输出中的函数、类型和关键字计数表明，MORPH 能够生成包含更丰富 SQL 特征的种子。其方言引导的 AQT 实例化与反馈修复旨在使复杂结构适配目标端和下游 fuzzer。

**修改尺度：** 保留特征数字及其反映的种子丰富度，删除错误的永久丢失归因。计数阶段和“有效输出”的定义按统计脚本核实，不在此重复完整流程无法隔离单个环节的说明。

### 24. Related Work：只改 SEDAR 句

**标签核实：** main.tex 的 Background and Motivation 节已有 `\label{sec:background}`，可保留该引用。

**拟改内容：** 仅在介绍 SEDAR 的原句中引用背景处的机制说明，避免重复展开。其后的通用概述与 MORPH 句保持原样，不在此重写相关工作定位。

### 26. 贡献列表：问题发现的概括

**现状：** 原贡献列表第一项将语法支持有限直接连到 query semantics 退化，与恢复机制容易冲突。

**计划：** 保留种子稀缺和迁移挑战的贡献，改为协调跨语句依赖、目标端可执行性和下游变异兼容性；不把现有 LLM 方法全部描述为必然简化查询或丢失语义。

## 五、待其他 RC

- **待 RC4：** SEDAR 实现来源及 reproduced/binary 措辞（原第 21 项来源部分）。
- **待 RC2/其他意见：** 摘要概括（原第 25 项）。
- **待 RC2/其他意见：** RQ1 案例的 high-fidelity/路径不可达表述（原第 28 项）。
- **待 RC2：** QTRAN 引用及其余相关工作定位，另行处理；Related Work 中 “rely on … post-generation filtering and sanitization” 的旧概括留待 RC2。
- **待其他 RC：** 方法流程与概述的其余措辞（原第 15、27 项），暂不处理。
- **待其他 RC：** 纯源码注释整理，暂不处理。

## 六、当前待办

- [ ] Reviewer B Comment 4 (b)：为每个分类提供证据，留待回复信列出文档链接或实验验证。

- [ ] 机制描述与错误归因的剩余修改：21、24、18、23；每次仅实施一项。
- [ ] 方法说明与贡献概括的剩余修改：13、17、26。
- [ ] 8–11、19 取得所列证据后再定稿。

## 七、英文原文与修改后源码完整对照

以下只保留第四节同号条目。修改后均为拟稿，尚未应用；24 只展示要替换的 SEDAR 一句，21 只补全恢复步骤。

### 7.8 Motivating Example：开头正文

**状态与范围：** 等第 8 项列出的案例证据；本轮不应用。

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

**状态与范围：** 等恢复记录和具体 SQL 图。motivating_rc1.pdf 仅为拟用文件名，尚未制作，不直接应用下面替换；图注另见第 10 项。

**修改前（当前 clean）：**

```latex
  \includegraphics[width=\linewidth]{media/motivating_71.pdf}
```

**修改后（拟稿，尚未应用）：**

```latex
  \includegraphics[width=\linewidth]{media/motivating_rc1.pdf}
```

### 7.10 动机图：备用图注和正式图注

**状态与范围：** 等最终案例图；只更正图注及其错误备用文字，保留其他 figure 代码。

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

**状态与范围：** 等恢复后的 SQL、错误及原因；核实前不应用。

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

### 7.21 RQ3：只补全恢复步骤

**范围：** 仅替换流程末尾的注释式清理描述，保留原句其他措辞。

**修改前（当前 clean）：**

```latex
We reproduced \sedar{}'s complete pipeline, including schema capture, end-to-end LLM translation, and its comment-out sanitization strategy.
```

**修改后（拟稿，尚未应用）：**

```latex
We reproduced \sedar{}'s complete pipeline, including schema capture, end-to-end LLM translation, and its mutability refinement, which temporarily comments out parser-incompatible fragments before mutation and restores them after mutation, before target-side execution.
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

### 7.24 Related Work：只改 SEDAR 句

**范围：** 仅替换以下一句；其后的通用概述和 MORPH 句不变。

**修改前（当前 clean）：**

```latex
\sedar{}~\cite{fu2024sedar} is the closest work to \tool{} because it also transfers existing DBMS test cases into fuzzing seeds.
```

**修改后（拟稿，尚未应用）：**

```latex
\sedar{}~\cite{fu2024sedar} is the closest work to \tool{} because it also transfers existing DBMS test cases into fuzzing seeds; its compatibility mechanism is described in Section~\ref{sec:background}.
```

### 7.26 贡献列表：问题发现

**状态与范围：** 保留种子稀缺和迁移挑战，避免将临时隐藏写成查询语义退化的根因。

**修改前（当前 clean）：**

```latex
\item We find that many DBMSs lack high-quality initial seeds for efficient fuzzing. LLM-based end-to-end translation often introduces errors and structural simplifications, and limited SQL grammar support further hinders mutation and degrades query semantics, reducing testing effectiveness.
```

**修改后（拟稿，尚未应用）：**

```latex
\item We find that many DBMSs lack high-quality initial seeds for efficient fuzzing. Transferring existing test cases requires coordinating cross-statement dependencies, target-side executability, and compatibility with downstream mutation interfaces.
```

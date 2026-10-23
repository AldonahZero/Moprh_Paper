# RC1 全文与图审计附录

审计日期：2026-10-23。

本文件归档此前的检索和图检查结果，不是执行计划。原编号仅用于追溯；已移交或删除的条目不在 RC1 执行。当前范围以 [plan.md](plan.md) 为准。

### 全文与图审计记录（2026-10-23）

关键词按大小写不敏感检索：`sanitiz|destructive|comment[ -]?out|logic loss|structural simplification|semantic depth|high.fidelity|unreachable`。共命中 **46 行：26 行正式源码、20 行纯注释**。另补查 simplify、degrades query semantics、comments/commenting 等变体和不含关键词的指定章节。**命中不等于必须改写**，正常组件名称、LLVM 插桩名称和已有结果也逐处登记为保留。

行号仅用于定位当前 main.tex，实际实施以第七节的原句为准。

| main.tex 行 | 类型 | 命中片段 | 处理归属 |
| --- | --- | --- | --- |
| 165 | 纯注释 | `% - Comment3 unsupported constructs：Feedback-Driven Adaptive Fuzzing 段写四级 fallback：已有 mapping -> RAG cand…` | 保留：MORPH 四级兜底总览注释 |
| 199 | 正文/图注/标签 | `(ii) Second, to satisfy strict grammar constraints imposed by downstream fuzzers, generated test cases ar…` | 第 2 项 |
| 201 | 纯注释 | `% (i)  syntactic or semantic errors due to limited understanding of target SQL dialects, (ii) structural …` | 历史注释记录 C2 |
| 253 | 纯注释 | `% This enables fuzzing to retain testing-relevant SQL features while avoiding the translation failures an…` | 历史注释记录第 3 项 |
| 290 | 正文/图注/标签 | `\item We find that many DBMSs lack high-quality initial seeds for efficient fuzzing. LLM-based end-to-end…` | 新增第 26 项 |
| 306 | 纯注释 | `% - Motivating example 必须写成失败过程：SedaR hallucination -> comment-out sanitization -> logic loss。` | 历史注释记录第 4 项 |
| 386 | 正文/图注/标签 | `Existing methods typically comment out unsupported features to support seed mutation after transfer, whic…` | 第 8 项 |
| 391 | 纯注释 | `% \caption{Motivating example: \sedar{} causes semantic deviation and logic loss, while \tool{} preserves…` | 第 10 项，随正式图注核对 |
| 392 | 正文/图注/标签 | `\caption{Conventional end-to-end translation methods like \sedar{} suffer from semantic error and structu…` | 第 10 项 |
| 413 | 正文/图注/标签 | `\textit{Limitations of Existing Approaches.} Figure \ref{fig:motivation} illustrates the semantic deviati…` | 第 11 项 |
| 419 | 正文/图注/标签 | `It then performs a high-fidelity instantiation into \duckdb{}’s equivalent syntax. By ensuring the type c…` | 第 13 项 |
| 420 | 正文/图注/标签 | `By maintaining these complex semantics during transfer, \tool{} ensures the generated seeds are effective…` | 第 13 项 |
| 441 | 正文/图注/标签 | `When a generated structure is rejected by the target parser or executor, \tool{} performs feedback-driven…` | 待其他 RC（原 27） |
| 652 | 纯注释 | `% 回应逻辑：unsupported constructs 先查已有 mapping，再用 RAG candidate，再 source DBMS 常量替换，最后才最小范围 comment out；这样回应“没…` | 保留：自身兜底顺序 |
| 654 | 纯注释 | `% - 挑战：fuzzer 支持的 grammar 通常小于 DBMS，直接 comment out 会破坏结构和测试价值。` | 历史注释记录 C5 |
| 655 | 纯注释 | `% - 四级 fallback 顺序必须清楚：已有 mapping -> RAG candidate -> source DBMS 执行后常量替换 -> 最小 comment out。` | 保留：自身兜底顺序 |
| 656 | 纯注释 | `% - 强调 comment out 是最后兜底，和 SedaR 式默认 sanitization 拉开距离。` | 历史注释记录第 14 项 |
| 661 | 纯注释 | `% - 写作顺序：先讲 fuzzer-compatibility challenge，再讲 feedback-guided adaptation，最后讲 sanitizer、fallback 和 mutable…` | 保留：本节写作顺序 |
| 666 | 纯注释 | `% As Figure~\ref{fig:feedback-driven} shows, upon receiving compatible seeds, \tool{} sanitizes and execu…` | 第 15 项顺序核对；不另作比较 |
| 673 | 正文/图注/标签 | `As shown in Figure~\ref{fig:feedback-driven}, compatible seeds are sanitized, executed in the target DBMS…` | 第 15 项 |
| 679 | 正文/图注/标签 | `\caption{Collaborative Workflow of Adaptive Query Sanitizing, Error-Guided Repairing, and Mutable Seed Ge…` | 审计结论：保留 |
| 685 | 纯注释 | `% \textbf{Error-Guided Sanitization.}` | 保留：源码小标题 |
| 686 | 纯注释 | `% Specifically, when a transferred seed $Q_{target}$ is fed into the fuzzer, it may still contain subtle …` | 历史注释记录第 16 项 |
| 687 | 正文/图注/标签 | `Specifically, when a transferred seed $Q_{target}$ still violates the fuzzer grammar, the sanitizer local…` | 保留具体修复说明；与第 15 项核对 |
| 694 | 纯注释 | `% When an error signal is received (e.g., ``Syntax error near SELECT''), \tool{} applies four fallbacks i…` | 历史注释记录 C6 |
| 695 | 正文/图注/标签 | `When an error signal is received (e.g., ``Syntax error near SELECT''), \tool{} applies four fallbacks in …` | 第 17 项，四级顺序保留 |
| 696 | 正文/图注/标签 | `Thus, destructive commenting is used only after structure-preserving repairs fail.` | 第 17 项 |
| 713 | 纯注释 | `% - RQ4 做 ablation，解释 AQT abstraction、Dialect-Aware AQT Instantiation、feedback sanitizer 各自贡献。` | 保留：消融目的注释 |
| 768 | 正文/图注/标签 | `Second, to quantify the effectiveness in improving fuzzing, we use LLVM's SanitizerCoverage to track \tex…` | 保留：LLVM SanitizerCoverage，非 SEDAR 清理机制 |
| 836 | 正文/图注/标签 | `\tool{}’s effectiveness stems from its structure-aware transfer. Take the case as an example, in \pg{}, n…` | 待 RC2/其他意见（原 28） |
| 918 | 正文/图注/标签 | `This proves that coverage gains are not merely statistical but functionally impactful, enabling the detec…` | 第 18 项 |
| 919 | 正文/图注/标签 | `Unlike \sedar{}, which sacrifices semantic depth through destructive sanitization, \tool{} employs struct…` | 第 18 项 |
| 941 | 正文/图注/标签 | `We reproduced \sedar{}'s complete pipeline, including schema capture, end-to-end LLM translation, and its…` | RC1 第 21 项仅补恢复；来源待 RC4 |
| 1001 | 纯注释 | `% \TODO{The diversity gap mainly comes from sanitization strategy: \sedar{} may comment out unparsable ca…` | 历史注释记录第 22 项 |
| 1056 | 纯注释 | `% - fuzzing phase：full vs. no feedback sanitizer，证明 high-quality generation 还需要 runtime feedback 才能转成 cov…` | 保留：组件消融目的注释 |
| 1065 | 正文/图注/标签 | `For the \textit{fuzzing phase}, since the \textit{Feedback-Driven Adaptive Sanitizer} operates independen…` | 保留消融设置；与第 15 项核对运行期反馈边界 |
| 1095 | 正文/图注/标签 | `\textbf{Contribution of Feedback-Driven Adaptive Sanitizer.}` | 保留：MORPH 组件名称 |
| 1097 | 正文/图注/标签 | `Table~\ref{tab:ablation_sanitizer} presents \tool{} covers 8\% to 39\% more branches across four DBMSs th…` | 保留：已有消融结果 |
| 1104 | 纯注释 | `% Table~\ref{tab:ablation_sanitizer} presents \tool{} covers 8\% to 39\% more branches across four DBMSs …` | 保留：备用消融结果注释 |
| 1110 | 正文/图注/标签 | `\caption{Sanitizer ablation on branch coverage (24h).}` | 保留：自身组件表题 |
| 1111 | 正文/图注/标签 | `\label{tab:ablation_sanitizer}` | 保留：LaTeX 标签 |
| 1127 | 纯注释 | `% \textbf{Answer to RQ4:} All components are critical: $\tool{}_{\textit{abs}}$ improves syntactic correc…` | 保留：备用消融结论注释 |
| 1133 | 正文/图注/标签 | `Furthermore, the \textit{Feedback-Driven Sanitizer} is indispensable for execution depth, boosting branch…` | 保留组件结果；不归因于 SEDAR 删除逻辑 |
| 1239 | 纯注释 | `% - LLM-Based Seed Generation：LLM 可用于直接合成、片段合成或 test-case transfer（Fuzz4All/ShQveL/Sedar），但常依赖文本生成和后处理 sa…` | 历史注释记录 C7 |
| 1268 | 正文/图注/标签 | `However, these methods largely rely on direct text generation or post-generation filtering and sanitizati…` | 第 24 项 |
| 1315 | 正文/图注/标签 | `It extracts dialect-agnostic AQTs, instantiates them through Dialect-Aware AQT Instantiation, and applies…` | 审计结论：保留 |

**扩展检查：**

| 位置 | 当前发现 | 处理 |
| --- | --- | --- |
| Abstract 130、139 | 注释含“语义结构被删”；正文含直接迁移简化结构/依赖的概括，但没有 SEDAR's destructive sanitization 原句 | 待 RC2/其他意见（原 25） + 历史注释记录 C1 |
| Introduction 210、216 | 备用文字仅提暂时注释，或仍推导测试逻辑丢失 | 历史注释记录 C3、C4 |
| Background 376 | 备用注释笼统描述 SEDAR 缺少跨语句依赖处理 | 历史注释记录 C8，与第 5 项一致 |
| 贡献列表 290 | 语法限制被直接连到 query semantics 退化 | 第 26 项 |
| RQ3 958 | 关于 schema-state propagation 的归因，未使用永久删除解释 | 纳入 RC2 的机制与证据核对，不机械改写 |
| Discussion 1136–1231 | 未发现 SEDAR 永久删除逻辑的比较；同模型解释仍需与实验口径一致 | 审计结论，当前不因 RC1 改写 |
| Threats to Validity | 当前 main.tex 没有独立同名章节 | 审计结论：记录缺项，局限正文落在第 17、19 项 |
| Conclusion 1315–1316 | adaptive sanitization 为自身组件，SEDAR 比较为覆盖/缺陷结果 | 审计结论：保留，指标/预算另行统一 |

**全部在用 PDF 图：** 8 张均提取文字；动机、overview、feedback-driven 和 case study 另做视觉核对。

| 图片 | 复核结果 | 处理 |
| --- | --- | --- |
| media/motivating_71.pdf | 临时注释被标为 Structural Simplification，且没有恢复阶段 | 第 9、10 项，补真实恢复结果 |
| media/overview-ase-new.pdf | Query Sanitizer/Error Fixer 为 MORPH 组件，没有 SEDAR 旧比较 | 保留，审计结论 |
| media/feedback-driven.pdf | 展示括号和类型修复，没有 SEDAR 破坏性清理描述 | 保留，顺序与第 15 项核对 |
| media/aqt_trans-1.pdf | Discarded 是方言 token 抽象标签，不是 SEDAR 最终删除查询逻辑 | RC1 不改，抽象定义与 RC2 核对 |
| media/rule.pdf | 展示 AQT 实例化与验证，没有 SEDAR 旧比较 | RC1 保留，示例/指标另行核对 |
| media/casestudy.pdf | 显示具体源/目标 SQL，没有 SEDAR 比较 | 图片保留，待 RC2/其他意见（原 28）修正文归因 |
| media/coverage_over_time_griffin_2x2.pdf | 结果曲线，无破坏性清理说明 | 保留 |
| media/ablation_combined_analysis.pdf | 组件消融，无 SEDAR 旧比较 | RC1 保留，Semantic Correctness 改名另行同步 |

**证据状态：** 本仓库未找到可确认 300 种子来源的 SQL 清单/运行日志、原动机恢复 trace 或实际 SEDAR 二进制；此结论仅限本地仓库，不推断实验机器上也不存在。已有 rebuttal 是待核实结果的来源，不能代替原始记录。

**设计描述与待核实推断：** [SEDAR 官方会议摘要](https://conf.researchr.org/details/icse-2024/icse-2024-research-track/210/Sedar-Obtaining-High-Quality-Seeds-for-DBMS-Fuzzing-via-Cross-DBMS-SQL-Transfer)明确描述临时注释及变异后恢复；[ACM 论文条目](https://doi.org/10.1145/3597503.3639210)用于定位原论文。本次尚未取得可直接核对的完整算法正文及评测实现日志。“未进入解析表示的隐藏文本不参与该表示上的变异”是有条件的设计推断，必须核查实际后端的输入与算子；[GRIFFIN 官方摘要](https://conf.researchr.org/details/ase-2022/ase-2022-research-papers/81/Griffin-Grammar-Free-DBMS-Fuzzing)还说明其有语句重组和元数据替换，不能把所有变异方式笼统称为语法树变异。MORPH 修复结构是否可变异还受可变异区域标记约束。

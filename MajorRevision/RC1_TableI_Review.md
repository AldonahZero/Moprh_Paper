# RC1 第 5 项与 Table I 核对报告

本轮完成 A、B、C；第 2 项正文未实施。下方同时提供相对本轮开始和相对 Git HEAD 的完整 main.tex diff；后者包含上一轮已写入的 SEDAR 两句定义。

## B0：修改前的原始标记

| 工具 | DDL | DML | DQL | Test Case Migration | Cross-stmt Deps. |
|---|---|---|---|---|---|
| SQLGlot | ✓ | ✓ | ✓ | × | × |
| jOOQ | ✓ | ✓ | ✓ | × | × |
| CrackSQL | △ | ✓ | ✓ | × | × |
| SEDAR | △ | △ | ✓ | ✓ | × |
| MORPH | ✓ | ✓ | ✓ | ✓ | ✓ |

表头 `Test Case` / `Migration` 合并对应 Test Case Migration；`Cross-stmt.` / `Deps.` 对应 Cross-stmt Deps.。

仅三个单元格改变：SQLGlot Test Case Migration ×→△；jOOQ Cross-stmt Deps. ×→△；CrackSQL DML ✓→△。修改前均不是 △。程序逐格比较确认其他单元格不变。

## B3：所有非注释正文命中

以下按源码顺序列出完整命中行，包含同一行的相邻句，避免截断上下文。表格行不属于正文句子；Table I 标记见上，两个结果表的九条数据行均未改。

### 1. main.tex:195 — 已做最小修改：primarily，并删除一概逐句独立处理的从句。

```latex
Existing SQL translation techniques~\cite{sqlglot,jooq,xie2026rise} primarily focus on statement-level translation. In contrast, DBMS fuzzing seeds are multi-statement test cases with rich cross-statement dependencies (e.g., schema references or type constraints). As a result, statement-level translation may break dependency chains, leading to execution failures when transferred across systems.
```

### 2. main.tex:279 — 已检查，无矛盾；未改。

```latex
To evaluate seed transfer quality, we also compare \tool{} against representative relevant techniques, including SQLGlot, jOOQ, CrackSQL, RISE, and \sedar{}.
```

### 3. main.tex:353 — 已做最小修改：primarily，并删除一概逐句独立处理的从句。

```latex
Specifically, existing SQL translation techniques~\cite{sqlglot, jooq, xie2026rise} primarily focus on statement-level translation. In contrast, fuzzing seeds are multi-statement test cases that include complex interactions among DDL, DML, and DQL statements.
```

### 4. main.tex:938 — 已检查，无矛盾；未改。

```latex
For translation effectiveness, we compare \tool{} against six baselines, including No translation, SQLGlot~\cite{sqlglot}, jOOQ~\cite{jooq}, CrackSQL~\cite{zhou2025cracking}, RISE~\cite{xie2026rise}, and \sedar{}~\cite{fu2024sedar}.
```

### 5. main.tex:940 — 已检查，无矛盾；未改。

```latex
SQLGlot and jOOQ represent off-the-shelf SQL dialect translators, while CrackSQL and RISE represent recent SQL translation systems.
```

### 6. main.tex:952 — 已检查，无矛盾；未改。

```latex
Existing translators improve syntactic acceptance but still leave a semantic gap; on DuckDB, SQLGlot, CrackSQL, and RISE reach at least 0.738 syntactic correctness, yet no more than 0.314 semantic correctness.
```

### 7. main.tex:959 — 已做最小修改：lack of → limited support for；其余从句未改。

```latex
As discussed in Section~\ref{sec:background} (Table~\ref{tab:translation-capability}), these low semantic scores stem from limited support for explicit cross-statement dependency preservation in statement-level translators such as SQLGlot, jOOQ, CrackSQL, and RISE, or from the lack of explicit schema-state propagation in \sedar{}, which can break type or reference consistency when DDL-created schema state is not propagated to subsequent statements.
```

### 8. main.tex:1290 — 已检查，无矛盾；未改。

```latex
General-purpose SQL dialect translators, such as SQLGlot~\cite{sqlglot} and jOOQ~\cite{jooq}, exploit the deterministic structure of SQL dialects and encode dialect mappings as parser or transpiler rules, making them useful for recurring statement-level conversions in query migration and application compatibility.
```

### 9. main.tex:1294 — 已检查，无矛盾；未改。

```latex
CrackSQL~\cite{zhou2025cracking} employs genetic algorithms alongside LLMs to mine complex mutation patterns.
```

## C：计划记录

7.5 已标记完成；7.21 已替换为用户指定的 mutability refinement 拟稿，尚未实施。Reviewer B Comment 4 (a)(c) 标记完成；(b) 的逐项证据留待回复信。

## 编译与版面

- git diff --check 和 git diff --cached --check 均通过。
- latexmk 因缺少 Perl 未能启动；改用 pdfLaTeX + BibTeX + 两次 pdfLaTeX 完成交叉引用和参考文献构建。
- 已检查 PDF 第 2、3 页：Table I、表注及新增段落无栏宽溢出；✓/△/× 正常显示。amssymb 已加载，沿用 cmark/partmark/xmark 宏。
- 最终日志无 Overfull、无未定义引用；仍有字体形状替代和 Underfull vbox 提示，BibTeX 有 pafl 条目缺少 booktitle 提示。
- 本轮修改前源码重新编译为 11 页，修改后为 12 页。两版参考文献均从第 11 页开始，新版延伸至第 12 页。未找到明确投稿页数上限，不能断言符合限制；未擅自压缩版面。
- 预览 PDF：../output/pdf/rc1-table-review.pdf。未覆盖原 main.pdf。

## 本轮 main.tex 完整 diff

```diff
--- a/main.tex (本轮开始)
+++ b/main.tex (本轮完成)
@@ -192,7 +192,7 @@
 While established DBMSs (e.g., \pg{}~\cite{pg_git}) possess extensive test suites that serve as ideal seeds, emerging engines like \duckdb{}~\cite{duckdb_git} often lack such assets, creating a critical bottleneck that limits the depth of testing for existing fuzzers.
 
 To construct initial seeds for these DBMSs, a common practice is to reuse test cases from other mature systems, but SQL dialect incompatibilities often hinder this process.
-Existing SQL translation techniques~\cite{sqlglot,jooq,xie2026rise} focus on statement-level translation, which treats SQL statements independently. In contrast, DBMS fuzzing seeds are multi-statement test cases with rich cross-statement dependencies (e.g., schema references or type constraints). As a result, statement-level translation may break dependency chains, leading to execution failures when transferred across systems.
+Existing SQL translation techniques~\cite{sqlglot,jooq,xie2026rise} primarily focus on statement-level translation. In contrast, DBMS fuzzing seeds are multi-statement test cases with rich cross-statement dependencies (e.g., schema references or type constraints). As a result, statement-level translation may break dependency chains, leading to execution failures when transferred across systems.
 
 Recent fuzzing-oriented approaches instead leverage large language models (LLMs) for end-to-end test-case transfer~\cite{fu2024sedar, lin2025qtran}. For example, \sedar{}~\cite{fu2024sedar} combines schema information and raw SQL text as input to an LLM to generate target-dialect seeds. However, such approaches still suffer from two fundamental limitations: 
 (i) First, due to limited understanding of target SQL dialects and weak structural awareness of multi-statement programs, LLM-based translation may introduce syntactic or semantic errors during generation, leading to invalid or inconsistent query behaviors.
@@ -331,10 +331,10 @@
     \textbf{Tool} & \textbf{DDL} & \textbf{DML} & \textbf{DQL} & \textbf{Test Case} & \textbf{Cross-stmt.} \\
     & & & & \textbf{Migration} & \textbf{Deps.} \\
     \midrule
-    SQLGlot~\cite{sqlglot} & \cmark & \cmark & \cmark & \xmark & \xmark \\
-    jOOQ~\cite{jooq} & \cmark & \cmark & \cmark & \xmark & \xmark \\
+    SQLGlot~\cite{sqlglot} & \cmark & \cmark & \cmark & \partmark & \xmark \\
+    jOOQ~\cite{jooq} & \cmark & \cmark & \cmark & \xmark & \partmark \\
     RISE~\cite{xie2026rise} & \partmark & \partmark & \cmark & \xmark & \xmark \\
-    CrackSQL~\cite{zhou2025cracking} & \partmark & \cmark & \cmark & \xmark & \xmark \\
+    CrackSQL~\cite{zhou2025cracking} & \partmark & \partmark & \cmark & \xmark & \xmark \\
     QTRAN~\cite{lin2025qtran} & \partmark & \partmark & \cmark & \xmark & \xmark \\
     \sedar{}~\cite{fu2024sedar} & \partmark & \partmark & \cmark & \cmark & \xmark \\
     \tool{} & \cmark & \cmark & \cmark & \cmark & \cmark \\
@@ -345,13 +345,14 @@
 \vspace{0.2em}
 \parbox{\columnwidth}{\scriptsize\raggedright
 \textit{Cross-stmt. Deps.} denotes explicit preservation of schema, data, and state dependencies across statements.
-\cmark: explicit support; \partmark: limited support; \xmark: not supported.}
+\cmark: supported as a documented capability; \partmark: partially supported (e.g., available for some statement types or through a separate component, but not as dependency-aware test-case transfer for fuzzing); \xmark: not supported.}
 \end{table}
 
 \textbf{Cross-Dialect Seed Transfer.} To address seed scarcity for emerging DBMS testing, cross-DBMS SQL transfer has emerged as an active research direction~\cite{zhou2020database}, but this is challenging due to SQL dialect differences.
-As shown in Table~\ref{tab:translation-capability}, most existing tools do not explicitly preserve cross-statement dependencies, making them unsuitable for test-case-level transfer in fuzzing.
-Specifically, existing SQL translation techniques~\cite{sqlglot, jooq, xie2026rise} focus on statement-level translation, which treats each SQL statement independently. In contrast, fuzzing seeds are multi-statement test cases that include complex interactions among DDL, DML, and DQL statements.
+As shown in Table~\ref{tab:translation-capability}, existing tools provide limited support for preserving cross-statement dependencies during test-case-level transfer for fuzzing.
+Specifically, existing SQL translation techniques~\cite{sqlglot, jooq, xie2026rise} primarily focus on statement-level translation. In contrast, fuzzing seeds are multi-statement test cases that include complex interactions among DDL, DML, and DQL statements.
 \sedar{}~\cite{fu2024sedar} transfers complete test cases by capturing source-side schema information and translating their statements with an LLM. To make the transferred seeds usable by downstream fuzzers, it temporarily comments out parser-incompatible fragments before mutation and restores them after mutation, before the mutated SQL is executed on the target DBMS.
+However, \sedar{} does not maintain translated target-side context across statements and leaves target-side execution-feedback regeneration as future work. \tool{} instead explicitly represents test structures and forward read-after-write dependencies in an AQT, coordinates instantiation with evolving target-side schema and type context, and uses parser and execution feedback to repair transferred seeds before downstream mutation.
 
 
 
@@ -955,7 +956,7 @@
 % Although \tool{} does not target result equivalence, retaining structural complexity through AQT-based abstraction and instantiation can incidentally preserve source-side behavior: among executable SQLite-to-DuckDB outputs, \tool{} matches source-side results in 49.78\% of cases, compared with 14.29\% for RISE.
 
 Compared with \sedar{}, \tool{} performs better in each DBMS, with \synatxratio{} higher syntactic correctness and \translateratio{} higher semantic correctness across the tested DBMSs.
-As discussed in Section~\ref{sec:background} (Table~\ref{tab:translation-capability}), these low semantic scores stem from the lack of explicit cross-statement dependency preservation in statement-level translators such as SQLGlot, jOOQ, CrackSQL, and RISE, or from the lack of explicit schema-state propagation in \sedar{}, which can break type or reference consistency when DDL-created schema state is not propagated to subsequent statements.
+As discussed in Section~\ref{sec:background} (Table~\ref{tab:translation-capability}), these low semantic scores stem from limited support for explicit cross-statement dependency preservation in statement-level translators such as SQLGlot, jOOQ, CrackSQL, and RISE, or from the lack of explicit schema-state propagation in \sedar{}, which can break type or reference consistency when DDL-created schema state is not propagated to subsequent statements.
 These gains confirm that Dialect-Aware AQT Instantiation maps source intent into target-dialect constraints while preserving parsability.
 % and executability.
 
```

## 相对 Git HEAD 的 main.tex 完整 diff

```diff
diff --git a/main.tex b/main.tex
index 5ea1c71..a1c5214 100644
--- a/main.tex
+++ b/main.tex
@@ -192,7 +192,7 @@ These fuzzers typically generate new test cases by repeatedly mutating a set of
 While established DBMSs (e.g., \pg{}~\cite{pg_git}) possess extensive test suites that serve as ideal seeds, emerging engines like \duckdb{}~\cite{duckdb_git} often lack such assets, creating a critical bottleneck that limits the depth of testing for existing fuzzers.
 
 To construct initial seeds for these DBMSs, a common practice is to reuse test cases from other mature systems, but SQL dialect incompatibilities often hinder this process.
-Existing SQL translation techniques~\cite{sqlglot,jooq,xie2026rise} focus on statement-level translation, which treats SQL statements independently. In contrast, DBMS fuzzing seeds are multi-statement test cases with rich cross-statement dependencies (e.g., schema references or type constraints). As a result, statement-level translation may break dependency chains, leading to execution failures when transferred across systems.
+Existing SQL translation techniques~\cite{sqlglot,jooq,xie2026rise} primarily focus on statement-level translation. In contrast, DBMS fuzzing seeds are multi-statement test cases with rich cross-statement dependencies (e.g., schema references or type constraints). As a result, statement-level translation may break dependency chains, leading to execution failures when transferred across systems.
 
 Recent fuzzing-oriented approaches instead leverage large language models (LLMs) for end-to-end test-case transfer~\cite{fu2024sedar, lin2025qtran}. For example, \sedar{}~\cite{fu2024sedar} combines schema information and raw SQL text as input to an LLM to generate target-dialect seeds. However, such approaches still suffer from two fundamental limitations: 
 (i) First, due to limited understanding of target SQL dialects and weak structural awareness of multi-statement programs, LLM-based translation may introduce syntactic or semantic errors during generation, leading to invalid or inconsistent query behaviors.
@@ -331,10 +331,10 @@ However, while mature DBMSs such as PostgreSQL provide rich test suites, many em
     \textbf{Tool} & \textbf{DDL} & \textbf{DML} & \textbf{DQL} & \textbf{Test Case} & \textbf{Cross-stmt.} \\
     & & & & \textbf{Migration} & \textbf{Deps.} \\
     \midrule
-    SQLGlot~\cite{sqlglot} & \cmark & \cmark & \cmark & \xmark & \xmark \\
-    jOOQ~\cite{jooq} & \cmark & \cmark & \cmark & \xmark & \xmark \\
+    SQLGlot~\cite{sqlglot} & \cmark & \cmark & \cmark & \partmark & \xmark \\
+    jOOQ~\cite{jooq} & \cmark & \cmark & \cmark & \xmark & \partmark \\
     RISE~\cite{xie2026rise} & \partmark & \partmark & \cmark & \xmark & \xmark \\
-    CrackSQL~\cite{zhou2025cracking} & \partmark & \cmark & \cmark & \xmark & \xmark \\
+    CrackSQL~\cite{zhou2025cracking} & \partmark & \partmark & \cmark & \xmark & \xmark \\
     QTRAN~\cite{lin2025qtran} & \partmark & \partmark & \cmark & \xmark & \xmark \\
     \sedar{}~\cite{fu2024sedar} & \partmark & \partmark & \cmark & \cmark & \xmark \\
     \tool{} & \cmark & \cmark & \cmark & \cmark & \cmark \\
@@ -345,13 +345,14 @@ However, while mature DBMSs such as PostgreSQL provide rich test suites, many em
 \vspace{0.2em}
 \parbox{\columnwidth}{\scriptsize\raggedright
 \textit{Cross-stmt. Deps.} denotes explicit preservation of schema, data, and state dependencies across statements.
-\cmark: explicit support; \partmark: limited support; \xmark: not supported.}
+\cmark: supported as a documented capability; \partmark: partially supported (e.g., available for some statement types or through a separate component, but not as dependency-aware test-case transfer for fuzzing); \xmark: not supported.}
 \end{table}
 
 \textbf{Cross-Dialect Seed Transfer.} To address seed scarcity for emerging DBMS testing, cross-DBMS SQL transfer has emerged as an active research direction~\cite{zhou2020database}, but this is challenging due to SQL dialect differences.
-As shown in Table~\ref{tab:translation-capability}, most existing tools do not explicitly preserve cross-statement dependencies, making them unsuitable for test-case-level transfer in fuzzing.
-Specifically, existing SQL translation techniques~\cite{sqlglot, jooq, xie2026rise} focus on statement-level translation, which treats each SQL statement independently. In contrast, fuzzing seeds are multi-statement test cases that include complex interactions among DDL, DML, and DQL statements.
-Recent work such as \sedar{}~\cite{fu2024sedar} explores fuzzing-oriented seed transfer using LLMs. While it improves automation by translating complete test cases, it still does not explicitly preserve cross-statement dependencies during migration.
+As shown in Table~\ref{tab:translation-capability}, existing tools provide limited support for preserving cross-statement dependencies during test-case-level transfer for fuzzing.
+Specifically, existing SQL translation techniques~\cite{sqlglot, jooq, xie2026rise} primarily focus on statement-level translation. In contrast, fuzzing seeds are multi-statement test cases that include complex interactions among DDL, DML, and DQL statements.
+\sedar{}~\cite{fu2024sedar} transfers complete test cases by capturing source-side schema information and translating their statements with an LLM. To make the transferred seeds usable by downstream fuzzers, it temporarily comments out parser-incompatible fragments before mutation and restores them after mutation, before the mutated SQL is executed on the target DBMS.
+However, \sedar{} does not maintain translated target-side context across statements and leaves target-side execution-feedback regeneration as future work. \tool{} instead explicitly represents test structures and forward read-after-write dependencies in an AQT, coordinates instantiation with evolving target-side schema and type context, and uses parser and execution feedback to repair transferred seeds before downstream mutation.
 
 
 
@@ -955,7 +956,7 @@ When the translated outputs are used as initial seeds for fuzzing, \tool{} furth
 % Although \tool{} does not target result equivalence, retaining structural complexity through AQT-based abstraction and instantiation can incidentally preserve source-side behavior: among executable SQLite-to-DuckDB outputs, \tool{} matches source-side results in 49.78\% of cases, compared with 14.29\% for RISE.
 
 Compared with \sedar{}, \tool{} performs better in each DBMS, with \synatxratio{} higher syntactic correctness and \translateratio{} higher semantic correctness across the tested DBMSs.
-As discussed in Section~\ref{sec:background} (Table~\ref{tab:translation-capability}), these low semantic scores stem from the lack of explicit cross-statement dependency preservation in statement-level translators such as SQLGlot, jOOQ, CrackSQL, and RISE, or from the lack of explicit schema-state propagation in \sedar{}, which can break type or reference consistency when DDL-created schema state is not propagated to subsequent statements.
+As discussed in Section~\ref{sec:background} (Table~\ref{tab:translation-capability}), these low semantic scores stem from limited support for explicit cross-statement dependency preservation in statement-level translators such as SQLGlot, jOOQ, CrackSQL, and RISE, or from the lack of explicit schema-state propagation in \sedar{}, which can break type or reference consistency when DDL-created schema state is not propagated to subsequent statements.
 These gains confirm that Dialect-Aware AQT Instantiation maps source intent into target-dialect constraints while preserving parsability.
 % and executability.
 
```

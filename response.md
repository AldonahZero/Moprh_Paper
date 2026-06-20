```markdown
# Response to Reviewers

We thank the reviewers for their valuable comments. Below is the response for major points.

## Reviewer A

### Question 1: What is the novelty compared to RISE?

Compared with RISE, Morph differs in terms of scope, challenges, and methodology.

**Scope:** Morph is a test-case-level SQL transfer approach for providing initial seeds for DBMS fuzzing, whereas RISE focuses on semantic-equivalent SELECT statement translation across RDBMSs.

**Challenge:** The challenge in Morph lies in preserving the richness of grammar constructs while ensuring test-case-level semantic correctness, while RISE needs to preserve semantic equivalence in statement-level translation.

**Methodology:** Morph first abstracts queries into AQT and instantiates them for the target DBMS, followed by feedback-based repair for fuzzer compatibility. AQT is not a SQL standard, but an intermediate representation based on AST enriched with semantic information like schema/clause/data dependencies and related constraints to ensure semantic correctness and preserve the grammar construct richness during instantiation. In contrast, RISE focuses on statement-level translation of SELECT queries. It adopts a skeleton-based decomposition within a single statement, isolates dialect-specific constructs, and uses LLMs to infer and apply translation rules. This statement-level design does not model cross-statement semantics and may introduce semantic errors in test-case-level transfer or contain fuzzer-incompatible constructs. For example, when translating `SELECT name FROM pragma_table_xinfo('t1')` from SQLite to DuckDB and `t1` does not exist, Morph preserves the original behavior by returning an empty result via `information_schema.columns` in AQT, whereas RISE translates it into `pragma_table_info('t1')`, which triggers an execution error in DuckDB.

Experimental results on SQLite→DuckDB show that RISE achieves 85.7% syntax correctness but only 31.4% semantic correctness, whereas Morph achieves 92.9% syntax correctness and 77.6% semantic correctness. Moreover, when used as initial seeds for fuzzing, Morph enables 34% more branch coverage than RISE and detects 3 bugs in DuckDB, while RISE fails to trigger any bugs.

### Question 2: What effort is required to extend to another DBMS?

Extending Morph to a target DBMS mainly involves two steps:  
(1) Automatically extract lexical/syntactic features from grammar specifications, and construct corresponding mapping rules;  
(2) For unmapped features, use LLMs to generate candidate rules followed by manual review and correction.

The following table shows that 3.2% (140/4323) of transfer rules require manual correction across six DBMSs, including two newly extended DBMSs Microsoft-SQLServer and Tencent-OpenTenBase. Each correction takes approximately 1–3 minutes for an engineer with Master-level DBMS expertise. For example, adapting Morph to SQLServer requires 3 manual corrections, taking less than 10 minutes in total.

| Target DBMS | Auto-Extracted Rules | Manually Corrected Rules | Manual-Corrected-Rules / Total-Rules |
|---|---:|---:|---:|
| DuckDB | 534 | 42 | 7.3% |
| ClickHouse | 1362 | 46 | 3.3% |
| MonetDB | 398 | 26 | 6.1% |
| Virtuoso | 782 | 22 | 2.7% |
| SQLServer | 230 | 3 | 1.3% |
| OpenTenBase | 877 | 1 | 0.1% |

### Comment 1: Evaluation Scale

We have extended Morph to Microsoft-SQLServer (a commercial DBMS) and OpenTenBase (a distributed DBMS), and discovered 44 previously-unknown bugs in total. Existing tools like SQLsmith cannot be directly adapted because SQLServer is closed-source and contains many vendor-specific grammars, while OpenTenBase introduces many distributed-specific grammars. Morph instead extracts specific rules from grammar and specification files, enabling PostgreSQL-to-SQLServer/OpenTenBase test-case transfer. It generates 233 rules for SQLServer (3 manually reviewed) and 878 rules for OpenTenBase (1 manually reviewed). Morph achieves syntax correctness of 96.6% and 98.6%, and semantic correctness of 71.5% and 72.7%, on SQLServer and OpenTenBase, respectively.

### Comment 2: Clarify Boundary Detection

A boundary is a structural split point in a SQL test case, such as CTE bodies or subqueries like `IN (SELECT ...)`. Morph identifies boundaries with two steps. First, Morph traverses the AST to detect independent query subtrees, such as CTE bodies. Second, it converts these subtrees into separate AQT units while recording their parent-child relationships, thereby preserving the original query scope and nesting structure.

### Comment 3: Unsupported SQL Dialects

Morph resolves unsupported dialects with two steps. It first checks the dialect dictionary and applies the corresponding mapping if available; otherwise, it uses RAG to retrieve a candidate replacement. If no suitable replacement exists, Morph executes the original construct in the source DBMS and substitutes it with the returned constant value. If this still fails, Morph comments out the construct. We will discuss it in the revision.

### Comment 4: Clarify Complexity Score

We will remove the complexity score in the revision.

### Comment 5: Clarify Semantic Correctness Metric

Semantic correctness measures whether transferred queries execute without errors in the target DBMS (not result equivalence), ensuring they are valid and mutable for fuzzers. In our experiments, Morph achieves 69%–77% semantic correctness, compared to 17%–39% for Sedar. Moreover, Morph does not aim to guarantee semantic equivalence. But through AQT-based abstraction and instantiation, it can preserve result equivalence to some extent; for example, in the SQLite-to-DuckDB setting, Morph achieves 49.78% among executable outputs, whereas RISE achieves 14.29%. Moreover, Morph preserves richer SQL features in the transferred seeds, including 411 more functions, 34 more data types, and 496 more keywords than Sedar.

### Comment 6: Presentation Issues

Thanks for your suggestion. We will fix the overflowing lines by adjusting the formatting, line breaks, and page layout in the revision.

## Reviewer B

### Question 1: Can Morph be clearly distinguished from a more carefully engineered Sedar?

The fundamental difference is that Sedar is an LLM-centric SQL text translation framework, whereas Morph is a structure-constrained, rule-guided instantiation framework. Sedar directly feeds schema and raw SQL into LLMs, relying on their generative capability for translation. In contrast, Morph reformulates SQL transfer as a constrained instantiation problem over AQT, which defines a structured semantic space with schema dependencies, nested structures, and clause-level relationships. Within this space, transfer rules are explicitly modeled, and the LLM is only used for instantiation under structural constraints.

Moreover, Morph introduces a feedback-driven structural repair mechanism that performs subtree-level correction on non-executable structures, ensuring fuzzer-compatible outputs. In contrast, Sedar directly discards such invalid structures, potentially losing useful seeds and reducing exploration opportunities. Experiments show that Morph achieves 69%–77% semantic correctness, compared to 17%–39% for Sedar across four DBMSs. Moreover, Morph further covers 14% more branches and finds 16 more bugs than Sedar in 24 hours.

### Question 2: What is the concrete manual effort required to support and maintain a new DBMS dialect?

Extending Morph to a target DBMS mainly involves two steps:  
(1) Automatically extract lexical/syntactic features from grammar specifications, and construct corresponding mapping rules;  
(2) For unmapped features, use LLMs to generate candidate rules followed by manual review and correction.

The following table shows that 3.2% (140/4323) of transfer rules require manual correction across six DBMSs, including two newly extended DBMSs Microsoft-SQLServer and Tencent-OpenTenBase. Each correction takes approximately 1–3 minutes for an engineer with Master-level DBMS expertise. For example, adapting Morph to SQLServer requires 3 manual corrections, taking less than 10 minutes in total.

| Target DBMS | Auto-Extracted Rules | Manually Corrected Rules | Manual-Corrected-Rules / Total-Rules |
|---|---:|---:|---:|
| DuckDB | 534 | 42 | 7.3% |
| ClickHouse | 1362 | 46 | 3.3% |
| MonetDB | 398 | 26 | 6.1% |
| Virtuoso | 782 | 22 | 2.7% |
| SQLServer | 230 | 3 | 1.3% |
| OpenTenBase | 877 | 1 | 0.1% |

### Question 3: Do the coverage and bug-discovery gains justify reduced automation compared with Sedar?

To evaluate the gains in coverage and bug discovery attributable to human effort, we removed the 42 manually reviewed rules (7.3% of 576 total rules, requiring approximately 50–70 minutes in total) and reran Morph on DuckDB. The results show that manual efforts enable Morph to discover 14,087 additional branches and 2 more bugs compared to the fully automated version. This shows that manual review provides additional gains, but most coverage and bug-finding capability is already achieved by the automated pipeline, demonstrating the robustness of Morph with minimal human effort.

### Question 4: Why do bug counts differ across RQ1 and RQ2?

The discrepancy comes from different time budgets: Table 2 uses one-week testing, while Table 4 reports 24-hour evaluation.

### Question 5: Which seed corpus was used for Squirrel in RQ2?

In RQ2, Squirrel used its default built-in initial seed corpus [https://github.com/s3team/Squirrel], rather than seeds transferred by Morph. We will clarify this setting in the revision.

### Comment 1: Relevance of SQLancer Comparison

We include SQLancer because it is a state-of-the-art DBMS testing tool for many DBMSs. Although SQLancer is mainly designed for logic bugs, it can also find crash bugs, and many DBMS fuzzing/testing papers compare with SQLancer in their experiments, e.g., SQUIRREL [CCS 2020], Griffin [ASE 2022], LEGO [ICDE 2023].

## Reviewer C

### Question 1: Why is semantic equivalence necessary for fuzzing seed generation?

Sorry for the confusion. Morph does not aim to guarantee semantic equivalence. Instead, it aims to preserve the structure and semantic richness, while also ensuring semantic correctness, enabling fuzzers to explore deeper DBMS components rather than only shallow paths. Morph achieves this via AQT-based structural abstraction and feedback-guided repair. AQT captures nested queries, functions, type casts, and cross-statement dependencies, while fuzzer feedback is used to repair incompatible constructs and ensure executability. Consequently, Morph preserves richer SQL features in the transferred seeds, containing 411 more functions, 34 more data types, and 496 more keywords, achieving 14% higher branch coverage, and detects 16 more bugs than Sedar in 24 hours.

### Question 2: Can existing SQL dialect translation approaches address fuzzing seed generation?

Existing SQL dialect translation tools cannot directly support fuzzing seed generation because they focus on statement-level translation and may produce fuzzer-incompatible SQL structures.  
(1) First, a test case typically consists of multiple interdependent statements, whereas existing tools, such as SQLGlot, perform rule-based dialect conversion at the single-statement level, and RISE focuses only on translating SELECT statements. Consequently, these statement-level approaches may lose cross-statement semantic dependencies, leading to semantic errors that prevent execution in target DBMSs and further hinder parsing and mutation by fuzzers. In contrast, Morph uses AQT to extract schema and dependency information at the whole-test-case level and leverages this information during instantiation.  
(2) Second, the grammar constructs supported by existing fuzzers are usually limited. Some translated structures may be incompatible with the fuzzer grammar, making the seeds unparsable or unmutable. Morph addresses this by incorporating fuzzer feedback during seed repair, so incompatible constructs can be rewritten into fuzzer-acceptable forms while preserving the original test-case structure.

### Question 3: How about comparing Morph's SQL translation with existing SQL dialect translation approaches?

We compare Morph against existing SQL dialect translation approaches under the same SQLite-to-DuckDB seed corpus. The results show that Morph achieves higher syntax correctness than SQLGlot, jOOQ, CrackSQL, and RISE by 19.1%, 27.7%, 18.9%, and 7.2%, and improves semantic correctness by 59.0%, 64.0%, 56.5%, and 46.2%, respectively. Therefore, seeds generated by Morph are more suitable for fuzzing. For example, when used as initial seeds, Morph enables 34.7% more branch coverage than RISE and detects 3 bugs in DuckDB, while RISE fails to trigger any bugs.

| Method | Type | Syntax Validity | Semantic Validity |
|---|---|---:|---:|
| SQLGlot | rule-based dialect translation | 0.738 | 0.186 |
| jOOQ | rule-based dialect translation | 0.652 | 0.136 |
| CrackSQL | LLM-based dialect translation | 0.740 | 0.211 |
| RISE | query-reduction-based translation | 0.857 | 0.314 |
| Sedar | fuzzing-oriented seed migration | 0.781 | 0.388 |
| Morph | structure-aware seed migration | 0.929 | 0.776 |

### Comment 1: How are dialect-specific constructs automatically identified during AQT extraction?

Dialect-specific constructs are identified by querying dialect rule database. If a node matches a dialect rule, it is marked as dialect-specific and abstracted in the AQT. Otherwise, Morph invokes the rule generator to synthesize a candidate abstraction rule and verifies it before adding it to the database. For example, if the database contains `AGE(x, y) → <TimeDiff>`, then `AGE(birth_date, CURRENT_DATE)` will be recognized as a PostgreSQL-specific construct and replaced with `<TimeDiff>`; unmatched nodes are kept unchanged.

### Comment 2: Are LLM-generated rules verified before being cached into the KB?

LLM-generated rules are verified before caching with two steps:  
(1) Apply the generated rule and validate it with the target DBMS parser; if parsing fails, repair it using the Error Fixer until validation succeeds;  
(2) Cache the rule into the knowledge base only after successful validation.

### Comment 3: Presentation Issues

Thanks for your suggestion. We will correct the algorithm number reference, fix the typo in the error-message example, unify the symbols in Figure 4, and revise the function-count difference to Table 6.
```

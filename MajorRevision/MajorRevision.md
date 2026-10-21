ICSE 2027 Paper #1111 Reviews and Comments
===========================================================================
Paper #1111 Structure-Aware Cross-Dialect SQL Transfer for DBMS Fuzzing
Seed Construction


Review #1111A
===========================================================================

Overall merit
-------------
3. Weak accept

Novelty
-------
3. Substantial - presents a clearly new idea, method, result, artifact,
   dataset, use case, workflow, or perspective

Rigor
-----
3. Good - appropriate methods and evidence adequately support the claims

Relevance
---------
4. Exceptional - potentially field-shaping, with broad or unusually strong
   impact

Verifiability and Transparency
------------------------------
3. Good - Sufficient information is provided to understand the work and
   support verification of the main claims

Presentation
------------
4. Excellent - highly clear, well structured, and effective in
   communicating the contribution

Paper summary
-------------
This paper presents MORPH, a framework for transferring multi-statement SQL test cases from mature DBMSs into initial seeds for fuzzing other DBMSs. MORPH represents each source test case using an Abstract Query Template that records normalized statement structures, read and write sets, dependency edges, and schema context. It then instantiates the template under the target dialect using retrieved dialect knowledge and bounded LLM generation. Finally, it uses parser and execution feedback to repair incompatible seeds and identify mutable regions for Griffin. The evaluation transfers tests from SQLite, MySQL, and PostgreSQL to MonetDB, Virtuoso, DuckDB, and ClickHouse. The authors report 14% higher branch coverage and 16 more bugs than SEDAR in the matched 24-hour campaigns. MORPH also discovers 28 previously unknown bugs, of which 20 have been confirmed and 14 fixed.

Strengths
---------
* The paper addresses an important practical problem in DBMS fuzzing.

* MORPH makes a meaningful technical advance over statement-independent seed transfer by introducing an explicit whole-test representation and evolving target-side context.

* The evaluation is extensive, covering four target DBMSs and 447,357 SQL statements from 6,875 source files.

* The real-world bug results are particularly strong. The developer confirmations and fixes provide convincing evidence of practical utility.

Weaknesses
----------
* The relationship with SEDAR and recent SQL translation systems is not characterized accurately.

* The independent role of the explicit dependency graph, beyond preserving the original order and propagating context, is insufficiently explained.

* The configurations and adaptations of several translation baselines are unclear.

* Some experimental and reproducibility information, including variance, campaign duration, and offline construction cost, is missing.

Detailed comments for authors
-----------------------------
### 1. Novelty

SEDAR is the closest predecessor because it addresses the same high-level problem of transferring existing DBMS test cases into fuzzing seeds. SEDAR already processes complete multi-statement test cases. It sequentially executes the source test, captures a referenced source-side sub-schema for each statement, translates individual statements, and restores their original order. Its mutability refinement also temporarily hides regions unsupported by the fuzzer parser during mutation and restores them before sending the resulting SQL to the target DBMS.

MORPH should therefore not be positioned as the first whole-test-case transfer framework or the first system to adapt transferred seeds to downstream fuzzers. Nevertheless, MORPH provides clear technical advances over SEDAR. In particular, it replaces independent statement-level translation with an explicit AQT representation, forward read-after-write dependencies, evolving target-side context, dialect-evidence-guided instantiation, and parser/execution-feedback repair. SEDAR does not maintain translated target context across statements and explicitly leaves target-side execution-feedback regeneration as future work. I consider these differences meaningful.

CrackSQL, RISE, and QTRAN provide relevant methodological precedents but address different tasks. CrackSQL already performs tree-based query decomposition, abstraction of translation-irrelevant content, target-syntax retrieval, localized translation, and operation-scope expansion. RISE reduces a complex query or procedural artifact, extracts a source-to-target AST rule, and applies the rule back to the original artifact. QTRAN uses dialect knowledge and RAG to translate an original query and regenerate its target-side mutant while preserving a metamorphic relation. These works show that structural decomposition, localized translation, and dialect-aware retrieval are not individually new. However, none of them models an independently executed stateful fuzzing test sequence, maintains target-side context across its statements, or integrates the result with coverage-guided seed mutation in the manner proposed by MORPH.

The related-work discussion contains several details that should be corrected. In particular, the paper states that CrackSQL employs genetic algorithms to mine mutation patterns, but the cited work instead uses functionality-based query processing, cross-dialect syntax embedding with contrastive learning, and local-to-global translation. In addition, describing RISE as strictly processing a single SQL statement is too narrow because it also evaluates complete stored procedures containing control flow and multiple DML statements. A more accurate distinction is that RISE processes one self-contained query or procedural artifact at a time rather than a test file containing an ordered sequence of independently executed statements.

Overall, the individual translation techniques have precedents, but their extension to explicit stateful seed transfer through AQT, target-context propagation, and feedback-driven repair constitutes a credible contribution.

### 2. Technical Clarity

The AQT is a useful abstraction for organizing a transferred test case. In particular, the combination of normalized statement templates, read and write sets, and evolving schema context provides more structure than independent statement-level translation. The context ablation, where execution success decreases from 65.1% to 52.4%, provides evidence that cross-statement context improves target-side executability.

However, the precise role and scope of the explicit dependency graph remain unclear. Algorithm 1 defines an edge from statement (i) to statement (j) when (i<j) and (R_j\cap W_i\neq\varnothing). This formalization captures forward read-after-write dependencies. Because every edge follows the original order, the original statement sequence is already a valid topological ordering, and the stable topological sort in Algorithm 2 normally preserves that order.

It is therefore unclear what ($G_{tc}$) contributes independently beyond recording the dependencies and preserving an order that is already present. The reported ablation removes context propagation rather than the graph itself, so it establishes the value of context but not an independent benefit from (G_{tc}). The paper should explain whether the graph affects retrieval, prompting, repair, or any other step not apparent from the algorithms.

The paper should also define the content and construction of the “object-state summaries” in (D) and clarify which forms of state are represented. The current formal definition explicitly supports forward RAW dependencies and cumulative schema/type context. Broader descriptions such as preserving all schema, data, and state dependencies should be narrowed unless additional dependency forms are implemented.

These issues do not invalidate the sequence-aware design, but clarifying them would make the technical contribution more precise.

### 3. Semantic Correctness

The paper explicitly states that its evaluation targets fuzzing-seed quality rather than source-to-target result equivalence. This is a reasonable design choice. For fuzzing, a transferred seed primarily needs to be executable on the target DBMS, retain useful SQL structures, and be compatible with downstream mutation. Full semantic equivalence is not necessarily required for this purpose.

However, the metric named “Semantic Correctness” only checks whether a seed executes on the target DBMS without a runtime error. This establishes execution validity rather than semantic equivalence. The distinction is important because repair operations such as constant substitution and minimal commenting can change test behavior.

I suggest renaming this metric to “Execution Validity” or “Runtime Validity” and narrowing statements concerning equivalent syntax, semantic-intent preservation, and high-fidelity transfer. A sampled source-to-target behavioral comparison would provide useful supplementary evidence.

### 4. Evaluation

The evaluation provides strong evidence that the complete MORPH pipeline produces practically useful seeds. In particular, the confirmed bugs and improvements over SEDAR demonstrate that the approach can exercise target DBMS code that remains difficult to reach with existing seeds.

My main concern is the configuration of the translation baselines. The original CrackSQL evaluation focuses on PostgreSQL, MySQL, and Oracle, while RISE evaluates PostgreSQL-to-MySQL and PostgreSQL-to-Oracle translation. MORPH applies these systems to MonetDB, Virtuoso, DuckDB, and ClickHouse. The paper should explain how each baseline was extended to these target dialects, including the dialect documents, knowledge bases, translation rules, LLMs, prompts, and manual effort used. It should also explain how a statement- or artifact-level translator was applied to a complete test sequence and what schema context, if any, was provided.

The SEDAR reproduction also requires clarification. The original SEDAR mutability-refinement algorithm temporarily hides fuzzer-parser-incompatible regions and restores them after mutation. The paper currently summarizes this process as comment-out sanitization and attributes structural simplification to it. The authors should confirm whether their reproduction includes the restoration step. If the motivating example is an observed failure in the reproduced system rather than the general SEDAR algorithm, this distinction should be stated explicitly.

The paper reports average coverage over five runs, but Tables III and VII provide no variance, confidence intervals, or statistical tests. Such information is important for stochastic fuzzing experiments. It is also unclear whether the reported bug counts are unions across the five runs or results from a particular run.

The full bug-finding campaign discovers 28 bugs, whereas the 24-hour comparison reports 22. The duration and computational budget of the full campaign should be reported. Similarly, Table VIII should specify the currency of the cost, the unit of time, and the total offline cost of processing all test files.

Finally, the component ablation is performed only for PostgreSQL-to-DuckDB transfer. Extending at least the most important ablation, particularly context propagation, to additional source-target pairs would strengthen the generality of the findings.

### 5. Relevance

The paper is highly relevant to the software engineering community. It connects software test reuse, program transformation, LLM-assisted translation, and coverage-guided fuzzing. The confirmed bugs show that the system addresses a practical testing problem rather than only improving intermediate translation metrics.

### 6. Verifiability and transparency

The paper reports the target DBMS versions, corpus size, primary model, temperature, hardware environment, and an anonymous artifact.

For full reproducibility, the paper should additionally document the baseline adaptations, source-target test allocation, repeated-run aggregation procedure, full campaign duration, and validation procedure for automatically generated translation rules. Table IX reports that 136 rules required correction.

### 7. Presentation

The overall workflow is clearly organized, and the separation into abstraction, instantiation, and feedback-driven fuzzing is easy to follow. The formal definition and algorithms help explain the intended representation.

Questions for authors’ response
-------------------------------
1. Can the authors evaluate source-to-target result or state equivalence?

2. What independent benefit does the dependency graph provide beyond context propagation?

3. How were CrackSQL, RISE, and SEDAR adapted and configured for the four target DBMSs, and how did the authors ensure a fair comparison?



Review #1111B
===========================================================================

Overall merit
-------------
2. Weak reject

Novelty
-------
2. Incremental - modest extension, adaptation, combination, or improvement

Rigor
-----
2. Fair - some weaknesses, but the main claims are at least partially
   supported

Relevance
---------
3. High - addresses an important SE problem and is likely to influence
   research or practice under clear assumptions

Verifiability and Transparency
------------------------------
2. Fair - Some information is provided, but not enough to support
   independent scrutiny, verification, or replication where appropriate

Presentation
------------
3. Good - clear, well organized, and easy to follow

Paper summary
-------------
This paper presents MORPH, a framework for transferring test cases from mature DBMSs into fuzzing seeds for target DBMSs. MORPH abstracts multi-statement SQL into AQTs that preserve query structures and dependencies, instantiates them using dialect knowledge and LLMs, and repairs incompatible constructs using parser and execution feedback. The generated seeds are then mutated by GRIFFIN. The evaluation reports coverage improvements and 28 previously unknown bugs on four DBMSs.

Strengths
---------
--Addresses an important practical problem: initial seed quality critically affects DBMS fuzzing effectiveness.

--Preserving schema context and cross-statement dependencies is more suitable for transferring complete test cases than statement-level translation.

--Empirical results show real-world impact: 28 new bugs found, 20 confirmed, 14 fixed.

Weaknesses
----------
--Limited novelty

--Incomplete related work

--Inaccurate characterization of SEDAR

--Unclear source of improvement: Coverage gains may stem from larger/richer seed corpus rather than AQT-based translation itself.

--Translation overhead unclear

Detailed comments for authors
-----------------------------
1. Limited novelty.
The paper positions AQT as a key contribution, but AQT largely resembles an existing SQL AST or intermediate representation augmented with abstract labels, read/write sets, dependency edges, and schema context. The authors do not clearly articulate what fundamentally new capability AQT provides over existing SQL representations (e.g., JOOQ's parse tree or SQLGlot's AST) or translation systems (CrackSQL, RISE, QTRAN). The distinction from these systems is insufficiently developed.

    More importantly, the evaluation lacks compositional baselines such as "existing SQL translator + SEDAR/GRIFFIN" or "AST-based translation + GRIFFIN". Without such ablations, it is difficult to determine whether the contribution is a fundamentally new transfer technique or mainly an engineering integration of existing components. The authors should add such baselines or provide a clearer theoretical argument for why AQT enables capabilities that prior representations cannot achieve.

2. Incomplete discussion of closely related work.
SmartFuzz (OOPSLA 2026) also studies high-quality seed generation for DBMS fuzzing and is directly relevant to the problem addressed by this paper. However, the paper neither discusses it sufficiently nor compares against it experimentally. The authors should add a discussion of SmartFuzz in Section VI (Related Work), clarify the differences in approach, and ideally include it as a baseline in the evaluation if feasible.

3. Potentially inaccurate characterization of SEDAR.
The paper characterizes SEDAR's comment-and-restore mechanism as "destructive sanitization" that "sacrifices semantic depth." However, SEDAR's mechanism is designed to temporarily hide target-specific constructs from downstream fuzzers' strict parsers during mutation and restore them afterward. This is a pragmatic compatibility strategy, not a destructive removal of query logic.

    The authors should revise their characterization to accurately reflect SEDAR's design intent and clarify the actual difference: MORPH performs pre-migration repair to make seeds parser-compatible, whereas SEDAR defers some compatibility handling until after mutation. A fairer comparison would evaluate both approaches under the same downstream fuzzer (e.g., GRIFFIN) and measure the net effect of each strategy on mutation effectiveness.

4. Questionable classification in Table I.
The classification of SQLGlot and jOOQ as entirely lacking "Test Case Migration" and "Cross-stmt Deps." support is overly absolute. In practice:

    SQLGlot can parse and transpile multi-statement SQL scripts.

    JOOQ's SQL Interpreter can maintain schema metadata across DDL and subsequent DML statements.

    Conversely, CrackSQL is marked as supporting DML, but this is not sufficiently validated by the paper or external references.

    The authors should: (a) clearly define what ✓, △, and × mean for each cell with specific criteria, (b) provide evidence for each classification (e.g., cite documentation or experimental verification), and (c) revise the table entries accordingly to avoid overclaiming.

5. Unclear source of the improvement.
The reported coverage and bug-finding improvements may stem from a larger or more diverse effective seed corpus rather than the AQT-based translation technique itself. For a fair comparison with SEDAR, GRIFFIN, and SQUIRREL, all approaches should use exactly the same source seed corpus and produce comparable numbers of final seeds that pass the target parser.

    The current experimental setup does not control for seed corpus size. The authors should: (a) report the number of valid seeds generated by each method before fuzzing, (b) normalize the corpus size across baselines, or (c) conduct an experiment where seed counts are equalized. This controlled setting is necessary to isolate the contribution of MORPH's SQL translation technique and demonstrate that improvements are not simply due to differences in seed quantity or diversity.

6. Translation overhead is unclear.
Table VIII reports "Cost/TS" and "Time/TS" but does not specify the unit of measurement (e.g., USD per test suite? seconds per test suite?) or the size of the test suite being translated. Additionally, the paper does not compare MORPH's end-to-end translation time and LLM cost with previous methods such as SEDAR.

    The authors should: (a) clearly define the units for cost and time, (b) report the average number of statements per test suite, (c) report per-seed translation time and cost, and (d) compare these overhead metrics against SEDAR and other baselines to demonstrate the practical feasibility of MORPH in real-world deployment.

Artifact assessment
-------------------
2. Partially Satisfactory, i.e., the artifacts partially contain what is
   declared in the submission form or the paper.

Comments on artifact assessment
-------------------------------
Bug evidence is insufficient. The bug links are inaccessible, and the triggering test cases are not publicly available. The artifact includes complete and executable source code.

Questions for authors’ response
-------------------------------
Q1. Could the authors clarify whether the reported coverage/bug-finding improvements primarily stem from AQT-based translation or from a larger/richer effective seed corpus? In a controlled experiment where all baselines use exactly the same number and source of initial seeds, how much of the 14% coverage gain over SEDAR remains attributable to AQT?

Q2. Regarding the characterization of SEDAR's comment-and-restore mechanism—if both MORPH and SEDAR ultimately feed compatible seeds to GRIFFIN, what is the exact difference in their post-mutation handling? Do seeds repaired by MORPH produce semantically equivalent mutated queries, whereas SEDAR's approach loses that equivalence? If not, what is the claimed advantage?



Review #1111C
===========================================================================

Overall merit
-------------
3. Weak accept

Novelty
-------
2. Incremental - modest extension, adaptation, combination, or improvement

Rigor
-----
3. Good - appropriate methods and evidence adequately support the claims

Relevance
---------
3. High - addresses an important SE problem and is likely to influence
   research or practice under clear assumptions

Verifiability and Transparency
------------------------------
3. Good - Sufficient information is provided to understand the work and
   support verification of the main claims

Presentation
------------
3. Good - clear, well organized, and easy to follow

Paper summary
-------------
Emerging DBMSs lack the built-in test suites that mutation-based fuzzers need as seeds, and reusing test cases from mature DBMSs fails across SQL dialects. MORPH abstracts a whole multi-statement test case into a dialect-independent Abstract Query Template (AQT) that preserves statement order and cross-statement dependencies, instantiates it into a target dialect using a hybrid knowledge base plus bounded LLM calls, and repairs leftovers using parser and execution feedback from the downstream fuzzer. On DuckDB, ClickHouse, MonetDB, and Virtuoso it reports 28 previously unknown bugs (20 confirmed, 14 fixed), 14% more branch coverage and 16 more bugs than SEDAR in 24 hours.

Strengths
---------
- Presents an important and well-motivated problem; the observation that translators operate at statement level while fuzzing seeds are multi-statement test cases with cross-statement dependencies is a good one.
- The paper and its approach both appear poised to make real impact. The paper uncovers 28 unknown bugs in heavily tested systems, 20 confirmed, 14 fixed, with MonetDB folding all reports into its NEXTRELEASE milestone. The DuckDB case study (a type-inference flaw reachable only when cross-table correlations survive) shows why structure preservation matters.
- Contains a thorough evaluation: four RQs, five repeated 24-hour runs, an ablation isolating context propagation (65.1% -> 52.4% when disabled), and a same-model control showing the gains over SEDAR come from the method rather than a stronger LLM. Manual-effort quantification (4.2% of 3,212 rules needed correction) is a quite nice data point to include.
- The AQT five-tuple and the ordered fallback chain directly address SEDAR's destructive sanitization.

Weaknesses
----------
- The "semantic correctness" metric means only "executes without runtime errors," not result equivalence; a seed that runs but computes something different still counts. Using "+130-450%" as a headline overstates what was measured.
- RQ2 baselines are somewhat hard to believe: zero bugs in 24 hours for SQUIRREL, GRIFFIN, and SQLancer across all four DBMSs. The baseline configuration is undocumented, several cells are "-", for unsupported targets, and the "528% over SQLancer" headline is computed over only the two DBMSs that SQLancer supports.
- Numeric inconsistency: the text claims RISE transfer improved semantic correctness "from 0.314 to 0.776," but Table V's own row reads RISE 0.314 / MORPH 0.748. One of these is wrong.
- Missing related work: SQLRight (USENIX Sec 2022) absent from RQ2; sqllogictest is never discussed; classic seed-selection literature ignored.
- What's novel here is the conjunction of approaches: AST abstraction + RAG + parser-feedback repair, which are established ideas assembled around the AQT.
- RQ1 (28 bugs, unspecified "full campaign") vs RQ2 (22 bugs in 24h) relationship and campaign duration are unclear.

Detailed comments for authors
-----------------------------
I would recommed tightening the validity claims: add a small differential check on transferred seeds (do they return equivalent results on a shared dialect subset?) to back the "semantic correctness" framing, and lean on coverage/bug counts rather than feature-richness as the primary evidence. For RQ2, publish baseline command lines and seed corpora in the artifact; the all-zero bug rows need more transparency, and SQLRight belongs in the comparison.



Rebuttal Response by Author [浩冈 毛 <maohaogang@126.com>] (1000 words)
---------------------------------------------------------------------------
## Common-Points
### Common-Point-1:Experimental Setup/Reporting(Reviewer A[Comment4]&B[Comment5]&C[Comment5])

**Statistics/Repeated-Run-Aggregation**:Coverage is averaged across five independent runs. For MORPH: variance=5.4M, standard deviation=2,324, 95% confidence interval=±2,900, p-value=0.032. Bug counts are the union across five runs after stack-trace deduplication and manual verification. 

**RQ1/RQ2 Campaign_Duration**:RQ1 found 28 bugs over seven days/target, while RQ2 found 22 bugs across five independent 24-hour runs.

**Cost/Time**:`Cost_currency`/`unit_of_time` are measured in `USD`/`seconds_per_seed`, respectively. MORPH costs $0.004 USD/seed, 219 s/seed, and $96 total_offline_cost across all evaluated DBMSs. In comparison, SEDAR costs $0.006 USD/seed, 146 s/seed, and $144 total_offline_cost.

**Baseline_Configuration**:Each baseline was configured with its `original_translation_mechanism` and required `target_dialect_knowledge`. Specifically, RISE/CrackSQL use official SQL_documentation, while SEDAR requires no additional dialect_knowledge. For fair comparison, all methods use the same source test-case corpus, follow their respective original_workflows, and introduce no additional manual translation_rules. Regarding `source-target test_allocation`, 5,879/4,551/5,850/6,990 test_cases were used for MonetDB/Virtuoso/DuckDB/ClickHouse, respectively. 

**Translation_Rule_Validation**:Translation_rules were validated by applying them to translated test_cases and checking the resulting_translations for errors. Rules producing incorrect translations were manually inspected and corrected. 


### Common-Point-2:Result/State Equivalence(Reviewer A[Question1]&C[Comment1])
On 100 sampled MySQL→DuckDB test cases, by preserving `cross-statement-dependencies` and `target-specific-constraints`, MORPH achieves 52.0%/74.0% result/state equivalence, compared with 14.0%/56.0%, 34.0%/65.0%, and 38.0%/69.0% for SQLGlot, RISE, and SEDAR, respectively. We will add a more comprehensive equivalence study and rename “Semantic Correctness” to “Execution Validity”.
| Method| Result_Eq.| State_Eq.|
| -------|---------:| --------:|
| SQLGlot|14.0%|56.0%|
| RISE |34.0%|65.0%|
| SEDAR|38.0%|69.0%|
| MORPH|52.0%|74.0%|

### Common-Point-3:Seed-Controlled Evaluation and Sources of Improvement(ReviewerB[Question1]&ReviewerC[Comment2])

**Seed-Normalized-Comparison:** We use the same number of initial_seeds for all methods(i.e.,  5,850/5,879/4,551/6,990 for DuckDB/MonetDB/Virtuoso/ClickHouse), and MORPH finds 14.5%/51.2%/68.1%more branches than SEDAR/Griffin/Squirrrel, indicating that the observed improvement comes from AQT rather than initial_seeds. 

|Target|SQUIRREL|GRIFFIN|SEDAR|MORPH|
|---|---:|---:|---:|---:|
|DuckDB|101,899|109,632|114,876|144,822|
|MonetDB|56,054|65,492|76,142|81,486|
|Virtuoso|20,082|27,115|36,583|40,183|
|ClickHouse|73,027|77,034|141,215|155,639|

**Controlled-Seed-Size Comparison:** Using randomly sampled 100/200/300 initial_seeds under the same environment and downstream_fuzzer (GRIFFIN) for 24h, MORPH achieves 65.2%/56.8%/47.3% and 32.4%/17.2%/19.2% higher coverage than SEDAR on DuckDB and MonetDB, respectively.  Moreover, SEDAR finds no bugs, while MORPH triggers a MonetDB bug with 200 and 300 seeds.  These results show that although seed quantity affects coverage, MORPH outperforms SEDAR under the same seed size.

|Method|DuckDB-100|DuckDB-200|DuckDB-300|MonetDB-100|MonetDB-200|MonetDB-300|
|---|---:|---:|---:|---:|---:|---:|
|SEDAR|49,745|58,938|68,499|20,406|28,173|29,948|
|MORPH|82,197|92,390|100,903|27,025|33,006|35,703|

---
## Reviewer A
### Question1:Result and State Equivalence
See-Common-Point-2.

### Question2:Benefits of Dependency Graphs

The dependency graph guides context selection during instantiation/repair. Without it, MORPH lacks explicit links for selecting dependency-relevant statements and object-state summaries for prompting. For example, in ALTER TABLE t ADD COLUMN b INT; SELECT b FROM t;, the edge selects the schema update for SELECT’s translation/repair. Summaries provide content, while edges guide selection.


### Question3:Baseline Configuration/Usage
See Common-Point-1 for configuration. For *Translator_Usage and `Schema_Context`*, CrackSQL/RISE translate statements individually and reassemble them in their original order without schema context, whereas SEDAR translates complete test cases using extracted source schemas as context.

### Comment1:Clarify CrackSQL/RISE
 
We will revise CrackSQL’s description to reflect its `functionality-based-query-processing`/`cross-dialect-syntax-embedding`/ `local-to-global-translation`,  and clarify that RISE processes one self-contained query or procedural_artifact at a time, rather than an ordered test sequence of `independently_executed_statements`. 

### Comment2:Clarify Object-State Summaries and Dependency Scope
 
Object-state summaries record `object_existence`, `table_definitions`, `column_names/types`, `constraints`, and `DDL_induced_schema_changes`. They are maintained incrementally in statement order by updating the metadata of affected objects to reflect DDL-induced changes. We will narrow our dependency claims to `forward RAW relationships` and `schema/type_context_propagation`.


### Comment 3:Clarify SEDAR’s Mutability-Refinement 

We used SEDAR’s original released binary and observed this issue in the motivating example. The restoration reintroduced the problematic construct, causing a semantic error. We will clarify that SEDAR temporarily comments out parser-incompatible regions and restores them after mutation. 


### Comment4:Statistical Reporting/Experimental Budget/Configuration Details 

See-Common-Point-1.


---
## Reviewer B
### Question1:AQT Contribution and Seed-Controlled Evaluation
See-Common-Point-3.

### Question2:Clarify Mutation_Handling in MORPH/SEDAR
MORPH performs pre-migration repair to make seeds parser-compatible, whereas SEDAR defers some compatibility handling until after mutation. Neither strategy guarantees semantic equivalence after mutation. On 100 sampled MySQL-to-DuckDB test cases, MORPH achieved higher result/state equivalence than SEDAR (52%/74% vs. 38%/69%). 

We sampled the same 300 translated seeds and applied MORPH's and SEDAR's respective `mutation_handling` with GRIFFIN for 24h. The results show that MORPH finds 27.2%/15.5% more branches than SEDAR on DuckDB/ MonetDB, respectively. We will revise our characterization of SEDAR and add the experiment.

### Comment1:AQT Novelty and Compositional Baselines 
AQT's novelty lies in *jointly modeling `cross-statement-dependencies` and `cumulative schema/type-context` for coordinated translation/repair*.  Unlike statement-level representations (e.g.,SQLGlot_AST) which primarily represent individual SQL_statements, AQT models the evolving state of an entire_test_sequence; unlike query-level translation systems (e.g.,CrackSQL/RISE/QTRAN), AQT uses preceding statements to guide the translation/repair of subsequent statements. 

We further pair different translators with the same downstream fuzzer(i.e.,GRIFFIN) for 24h, MORPH finds 33.6%/16.7%/26.1% more branches, 3/3/2 more bugs than `SQLGlot+GRIFFIN`/`RISE+GRIFFIN`/`SEDAR+GRIFFIN`, demonstrating the effectiveness of MORPH's translation approach. 


|Method|Branch Coverage|Unique Bugs|
|---|---:|---:|
|SQLGlot + GRIFFIN|108,385|0|
|RISE + GRIFFIN|124,147|0|
|SEDAR + GRIFFIN|114,876|1|
|MORPH + GRIFFIN|144,822|3|

### Comment2:Comparison with SmartFuzz
 
**Goal:** SmartFuzz focuses on generating new DBMS-specific seeds, whereas MORPH focuses on transferring existing test cases across DBMSs.

**Methodology:** SmartFuzz leverages DBMS documentation and historical bug-triggering inputs, while MORPH uses cross-statement dependencies and cumulative schema/type context for translation and repair.

**Experiment:** Using randomly sampled 300 seeds from each method as GRIFFIN's initial_seed_corpus in 24h, SmartFuzz achieves 81,090 covered branches, compared with 100,903 for MORPH. 

### Comment3:Clarify Mutation_Handling in SEDAR

See-ReviewerB-Question2. 


 ### Comment4:Clarify TableI Classification
We revise SQLGlot’s `Test_Case_Migration` to △ for multi-statement transpilation, revise jOOQ to △ for schema-state tracking, and revise CrackSQL’s DML support to △.

### Comment5:Translation Cost/Time
See-Common-Point-1.

### Comment6:Bug Evidence
We have added the triggering test cases to the [artifact](https://anonymous.4open.science/r/MORPHArtifact-8E44/README.md) for bug verification/reproduction.  Bug-report_links will be released after acceptance due to the double-blind_review. 
---
## Reviewer C
### Comment1:Validity Claims and Result Equivalence

See-Common-Point-2. 

### Comment2:RQ2 Baseline Configuration/Comparison  
**Baseline Configuration.** We use the default `configurations`/`seed_corpora`/`command_lines` of SQUIRREL, GRIFFIN, and SQLancer from their official repositories, and include them in the [artifact](https://anonymous.4open.science/r/MORPHArtifact-8E44/README.md). See Common-Point-3 for the same-seed-budget comparison. The zero-bug results reflect that previously reported bugs were fixed in the newer DBMS versions, with no new bugs found. 

**SQLancer/SQLRight Comparison:** We will explicitly clarify that the reported 528% improvement over SQLancer is computed only on commonly supported DuckDB/ClickHouse. For SQLRight, MORPH covers approximately 371.3%more branches on DuckDB within 10h.
### Comment3:Writing Issues
The correct value is 0.748, not 0.776. We will correct it.
 
### Comment4:Add Related-Work
We will add SQLRight/sqllogictest/seed-selection-approaches(e.g., AFLFast) to Related_Work and compare with MORPH: SQLRight→coverage-guided DBMS-fuzzing for logic bugs; sqllogictest→cross-engine result_validation; AFLFast→seed prioritization/energy_scheduling in fuzzing; MORPH→cross-DBMS test-case transfer for DBMS-fuzzing. 

### Comment5:Clarify RQ1/RQ2 Campaigns
See-Common-Point-1.



Review #1111D
===========================================================================

Metareview
----------
Thank you for submitting your manuscript to ICSE 2027. 

MORPH addresses an important practical problem and demonstrates strong empirical results. The rebuttal addresses several experimental concerns through additional controlled comparisons and clarifications. However, concerns regarding technical novelty and positioning against related work remain.

Following the discussion, the reviewers converge on a Major Revision recommendation, with the following required changes:

1. Correct the description of SEDAR's comment-before-mutation and restore-after-mutation mechanism. Clearly explain the actual methodological differences between MORPH and SEDAR, avoiding misleading characterizations of the latter.

2. Moderate the novelty claims and better distinguish MORPH from existing SQL translation approaches, particularly SEDAR, RISE, QTRAN, and CrackSQL. Explain more concretely what AQT and its associated mechanisms contribute beyond existing techniques, especially in the context of DBMS fuzzing.

3. Clarify how MORPH differs from and complements SmartFuzz in seed construction. The additional comparison is useful but limited in its experimental scope. The revision should acknowledge these limitations and avoid overstating comparative advantages.

4. Clarify the provenance of the SEDAR binary used in the experiments, particularly given conflicting reports about its public availability. Provide sufficient information about the artifact and configuration to support reproducibility.

Overall, the reviewers recognize the practical significance of MORPH. The revision should focus on establishing a more accurate and well-supported account of its technical contributions and relationship to prior work.

Recommendation
--------------
2. Major Revision
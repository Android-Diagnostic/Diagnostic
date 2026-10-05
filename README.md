# Beyond Application Errors: Uncovering Diagnostic Clues in Third-Party Library Logs

This directory contains the runtime logs, processed data, similarity results, and manual annotations used in the paper. The study analyzes application fault logs and third-party library (TPL) logs from 1,000 Android applications.

## Abstract

Android applications interleave application-generated logs and third-party library logs (TPLs) in the same Logcat stream, yet little is known about whether these heterogeneous sources provide distinct diagnostic evidence during failures. We conduct a large-scale empirical study of 1,000 Android applications, analyzing 69.9 million raw Logcat lines and 908,882 fault-related application logs through lexical similarity, sentence embeddings, manual coding, and LLM-based diagnosis. We find that relationships between application faults and nearby TPLs are sparse and highly localized: only 1.61% of comparable application logs exhibit relatively high lexical overlap with a nearby TPL, and only 3.02% have a highly similar TPL under three-model semantic consensus. Yet when semantically related TPLs are identified, they are rarely redundant. Among 258 highly ranked cases examined manually, 94.19% provide complementary diagnostic information; among these com- plementary cases, 93.00% contribute fault-localization or cause/trigger evidence. TPL evidence also affects downstream LLM-based diagnosis. Across 612 application-level cases, adding TPL context increases at least one diagnostic dimension in 75.8% of cases, with the largest gains in execution/runtime context (49.0%) and post-failure behavior (49.3%). However, only 48.0% of cases improve without a decrease in another dimension, and manual analysis shows that 58.6% of observed score decreases reflect measurement instability rather than substantive information loss. These results reveal a sparse-but-informative structure in heterogeneous runtime logs: diagnostically useful TPLs constitute only a small fraction of the surrounding log stream, but when identified, they can expose evidence absent from the application error and substantially alter downstream diagnosis. Our findings therefore motivate provenance-aware diagnostic systems that selectively retrieve and structure relevant TPL evidence rather than treating all nearby logs as additional context.

## Annotation Convention

The two human annotators are anonymized as **A1** and **A2**. Columns associated with A1 and A2 contain their labels before discussion. Columns ending in `final`, or the `Final_reason` column, contain the adjudicated labels used in the paper.

## Top-Level Data

- **`manual_log_1000.zip`**: The 1,000 raw Logcat files collected from the Android applications. Each filename contains the application package name and collection timestamp.
- **`raw_logs_file_names.txt`**: Manifest of the 1,000 files in `raw_logs/`.
- **`selected_logs.csv`**: Parsed application-level logs after process attribution. It contains log components, content and templates, final TPL provenance (`TPL_final` and `Library_Final`), and fault-related fields (`FaultWords` and `FaultSource`).

## RQ1: Lexical Similarity

- **`RQ1/MASTER_TFIDF_N_to_Y_parallel_within_1_0s.csv`**: TF-IDF similarity results for fault-related non-TPL target logs. It records nearby TPL candidates within the same application's +/-1-second window, with the best and average TF-IDF similarities.

## RQ2: Semantic Similarity

The three files have the same schema and differ only in the sentence-embedding model used. Each file records the surrounding TPL candidates, best semantic match, and neighborhood-average similarity for every fault-related non-TPL target log.

- **`RQ2/all_MiniLM_L6_v2_original_logs_with_TPLN_window_avg_similarity_1_0s.csv`**: MiniLM semantic-similarity results.
- **`RQ2/BAAI_bge_large_en_v1.5_original_logs_with_TPLN_window_avg_similarity_1_0s.csv`**: BGE semantic-similarity results.
- **`RQ2/jinaai_jina_embeddings_v2_base_code_original_logs_with_TPLN_window_avg_similarity_1_0s.csv`**: Jina semantic-similarity results.

## RQ3: Complementary Diagnostic Information

RQ3 combines the top-ranked cases from BGE, Jina, and MiniLM and removes duplicates, producing 308 cases.

- **`RQ3/RQ3_50_example.csv`**: The 50 cases used to develop the RQ3 codebook. These cases were discussed jointly and were excluded from the quantitative results.
- **`RQ3/RQ3_remaining_cases.csv`**: The remaining 258 cases independently annotated by A1 and A2. `Label1_final` records the final relationship label, and `Label2_final` records the final diagnostic-information category.


## RQ4: LLM-Based Diagnosis

RQ4 compares diagnoses generated from the application log alone with diagnoses generated from the application log plus TPL context. Scores and evidence are reported for failure characterization, fault localization, cause/trigger, execution/runtime context, and post-failure behavior.

- **`RQ4/RQ4_40_example.csv`**: The 40 cases used to develop the codebook for diagnostic-score decreases. Cases with decreases in multiple dimensions occupy multiple rows, producing 48 dimension-level records. These cases were excluded from the quantitative manual-analysis results.
- **`RQ4/RQ4_remaining_cases.csv`**: Manual annotations for the remaining 218 cases with score decreases. Multiple decreases are coded separately, producing 275 dimension-level records. `A1_reason` and `A2_reason` contain the independent labels; `Final_reason` contains the adjudicated label used in the paper.
- **`RQ4/minilm0917_highest_best_selected_columns.csv`**: It contains one target log per eligible application log file: the target with the highest MiniLM best similarity, its surrounding TPL logs, the best-matching TPL log, and the similarity score.
- **`app_log_diagnosis_model.txt`: Reproducibility metadata for the app-only diagnostic analysis of 612 application logs. It records the model, prompts, invocation settings, call statistics, token usage, estimated cost, execution time, score distributions, and output schema.
- **`app_TPL_model_info.txt`: Reproducibility metadata for the App+TPL diagnostic analysis of the same 612 cases. It records the model, prompts, invocation settings, validation and retry procedure, call statistics, token usage, estimated cost, execution time, and output schema.
- `prompt.txt`: The prompt used to evaluate each RQ4 case across five diagnostic-information dimensions using scores from 0 to 2 and supporting log evidence.

## Naming Notes

- `TPLN` denotes a non-TPL target log (`TPL_final = N`).
- `TPLY` denotes a surrounding TPL candidate (`TPL_final = Y`).
- `best` denotes the highest similarity to any TPL candidate in the temporal window.
- `avg` denotes the average similarity over the retained surrounding TPL candidates.

 Genomic Evidence Stability (GES)

A metadata-derived evidence stability index for studying uncertainty, temporal instability, and evidence handling in ClinVar-based biomedical AI.

Genomic Evidence Stability (GES) is a research framework for making knowledge-source uncertainty explicit in genomic AI systems. Rather than treating every ClinVar record as equally reliable or static, GES summarizes interpretable metadata signals such as recency, submitter support, review confidence, conflict status, and classification entropy.

GES is not a calibrated clinical probability, patient-level prediction, or therapeutic decision rule.
It is a metadata-derived evidence index intended for research on relative evidence fragility, prioritization, longitudinal monitoring, and evidence-aware AI.

 Motivation

Biomedical AI systems often consume curated genomic knowledge as if it were fixed ground truth. In practice, ClinVar is a living resource:

variant interpretations can change over time;

submitters can disagree;

review strength varies across records;

evidence may become stale;

uncertainty may be hidden from downstream retrieval or decision-support pipelines.

GES studies this problem at the knowledge-record level.

The central research question is:

Can metadata available at an earlier ClinVar snapshot help identify records that are more likely to undergo future evidence instability?

A second question asks whether evidence-quality signals can improve how retrieval-augmented generation (RAG) systems handle uncertainty.

⚠️ Core Concept

GES should be interpreted as:

a metadata-derived index summarizing relative evidence support, recency, disagreement, and review strength

The original prototype used weak supervision to construct clear-case proxy labels from ClinVar metadata and fitted an interpretable logistic-regression model.

Because those proxy labels were created from the same metadata families used by the model, near-perfect in-sample discrimination against the proxy labels is structurally expected and is not evidence of external predictive validity.

The current project therefore evaluates GES primarily through independent temporal validation against later ClinVar behavior.

📊 Data and Study Design

Target genes

The current validated study focuses on:

BRCA1

BRCA2

MLH1

EGFR — treated as an exploratory somatic-oncology stratum in the longitudinal analysis

Frozen temporal design

The principal longitudinal study uses condition-specific ClinVar RCV records:

T0: January 2023 ClinVar archive

embedded cutoff: 31 December 2022

71,659 target-gene RCV records

T1: January 2026 ClinVar archive

embedded cutoff: 27 December 2025

100,920 target-gene RCV records

Locked evaluable temporal cohort: 66,636 RCVs

Prespecified future-instability events: 6,485

Observed event prevalence: 9.73%

The future outcome was frozen independently of GES scoring and captures knowledge-record changes such as:

material transitions among benign/likely benign, VUS, and pathogenic/likely pathogenic groups;

emergence of unresolved aggregate conflict;

resolution of a previously conflicting record to a materially different classification group.

This outcome measures future ClinVar knowledge-record instability, not clinical correctness or patient outcome.

🧠 GES Features

The longitudinal GES implementation uses interpretable metadata-derived features including:

Recency score

Recency-missingness indicator

Submitter diversity/support

Review confidence

Aggregate conflict status

Normalized classification entropy

A weak-supervision layer constructs training labels from baseline metadata. The fitted model is an L2-regularized logistic-regression pipeline.

The resulting bounded score is retained as a relative metadata-derived stability index. Its numerical value should not be interpreted as a calibrated probability that a ClinVar record is clinically correct or will remain unchanged.

⏳ Independent Temporal Validation

The main scientific evaluation asks whether the frozen T0-only GES ranking identifies records that later satisfy the prespecified T1 instability outcome.

Principal locked results

Metric

Full GES

AUPRC

0.1124

AUROC

0.5358

Future-instability prevalence

0.0973

Interpretation:

GES shows a weak but measurable temporal ranking signal.

It modestly exceeds review stars on the primary AUPRC comparison.

It does not outperform the stronger prespecified combined-metadata heuristic on primary AUPRC.

Absolute calibration is poor.

The inherited score threshold should not be treated as a validated clinical decision boundary.

Performance varies across genes; EGFR shows poor transport and remains exploratory.

These findings support a narrow interpretation of GES as a relative evidence-uncertainty / prioritization signal, not as a clinical probability model.

🧪 Sensitivity and Robustness Analyses

The repository includes prespecified and frozen analyses covering:

paired bootstrap uncertainty;

gene-level inference;

alternative future-instability outcomes;

leave-one-gene-out validation;

frozen-threshold bootstrap support;

primary event-component analysis;

exact-link sensitivity;

low-score enrichment;

metadata data-quality perturbations.

Data-quality perturbation study

A reviewer-driven robustness analysis re-evaluates the same frozen T0 model under 15 prespecified metadata-quality perturbation scenarios, including:

missing recency;

missing submitter information;

missing review-confidence information;

missing entropy information;

conflict false positives and false negatives;

review-status downgrading;

submitter undercounting;

artificial recency staleness;

combined metadata loss.

The model is not retrained, the future outcome is not redefined, and no T1 information is used to repair perturbed metadata.

The perturbation results are heterogeneous, reinforcing the view that GES is a metadata-sensitive evidence index rather than a clinically calibrated probability.

🤖 Stability-Aware Genomic RAG Experiment

A separate controlled experiment evaluates whether evidence-quality reranking changes structured genomic RAG behavior.

Frozen design

100,920 score-blind T1 evidence packets

80 prespecified genomic questions

6 retrieval/reranking conditions

shared semantic top-20 candidate pool

final top-5 evidence context

480 question-condition prompts

3 fixed-snapshot generations per prompt

1,440 structured-valid LLM responses

paired question-level bootstrap inference

Main result

Full-GES reranking did not show a clear improvement in the prespecified overall automated evidence-fidelity composite relative to semantic-only retrieval.

A prespecified secondary endpoint showed improved caution-policy concordance under Full-GES reranking, suggesting that evidence-stability signals may be more useful for qualification, caution, abstention, or response-policy governance than as a universally superior retrieval score.

This experiment does not establish clinical safety or factual superiority of generated answers.

📚 Key Notebooks

Core GES development and temporal validation

01_evidence_drift_full_pipeline.ipynb — original cross-sectional GES prototype

02_GES_temporal_validation.ipynb — historical temporal-validation setup

03_GES_future_instability_outcomes.ipynb — frozen future-instability outcome construction

04_GES_locked_temporal_validation.ipynb — locked T0→T1 temporal evaluation

Temporal-validation support analyses

GES_Stage6C_Cell_6C_4F0_Gene_Level_Inference_Materialization.ipynb

GES_Stage6C_Cell_6C_4H0_Alternative_Outcome_Secondary_Drift_Materialization.ipynb

GES_Stage6C_Cell_6C_4J0_Leave_One_Gene_Out_Validation_Materialization.ipynb

GES_Stage6C_Cell_6C_4K0A_Frozen_Threshold_Bootstrap_Support_Materialization.ipynb

GES_Stage6C_Cell_6C_4K0B_Primary_Event_Component_Bootstrap_Support_Materialization.ipynb

GES_Stage6C_Cell_6C_4K0_Final_Integrated_Package_Freeze_FINAL_CORRECTED_V2.ipynb

Stability-aware RAG experiment

The RAG workflow is implemented across the 05_... through 23_... notebooks, including score-blind evidence construction, biomedical semantic retrieval, six-arm quality reranking, frozen prompt materialization, fixed-snapshot LLM generation, blinded structured evaluation, condition restoration, and paired bootstrap analysis.

The final prespecified RAG analysis is:

23_GES_Aware_Genomic_RAG_Cell_7C16_Prespecified_Experiment2_RAG_Performance_Analysis.ipynb

Reviewer-driven robustness and reproducibility

GES_Stage6D_Cell_6D_1A0_Reviewer_Data_Quality_Perturbation_Robustness_COLAB.ipynb

GES_Stage6D_Cell_6D_1B0_Reference_Authenticity_Claim_Support_Audit_COLAB.ipynb

GES_Stage6D_Cell_6D_1B1_Authoritative_Reference_Resolution_and_Claim_Map_COLAB.ipynb

GES_Stage6D_Cell_6D_1C0_Final_Springer_Revision_Evidence_Package_Freeze_COLAB.ipynb

GES 3.0 historical archive extension

The GES3_* notebooks are an ongoing historical ClinVar archive-normalization extension. They are separate from the frozen January 2023 → January 2026 primary temporal-validation analysis described above.

🔁 Reproducibility

This repository emphasizes fail-closed execution, frozen analysis artifacts, deterministic seeds, explicit protocol checkpoints, and SHA-256 manifests.

Key reproducibility practices include:

frozen T0 and T1 study definitions;

condition-specific RCV linkage;

prespecified future-outcome construction;

no T1-informed model fitting for the primary temporal analysis;

deterministic seeds;

paired bootstrap resampling;

protocol amendment records for the RAG experiment;

artifact-level SHA-256 verification;

environment and package manifests in revision-support notebooks.

Some downstream notebooks expect previously materialized artifacts in the project workflow and are not intended to be run independently without the preceding stages.

ClinVar source data are publicly available from NCBI:

https://ftp.ncbi.nlm.nih.gov/pub/clinvar/

For publication-level reproduction, use the exact archived release/cutoff information recorded in the temporal-validation notebooks rather than substituting the current live ClinVar release.

📈 Figures

The figures in this repository and the associated manuscript workflow are author-created from project analyses.

Legacy cross-sectional figures are retained for transparency and for documenting the original prototype. They should not be interpreted as independent validation of GES.

⚠️ Clinical and Scientific Boundaries

This repository is for research use only.

GES has not been validated for:

patient-level diagnosis;

therapy selection;

treatment eligibility;

prognosis;

clinical risk prediction;

autonomous clinical decision support;

patient-safety assessment.

Before any evidence-stability index could support therapeutic decision-making, additional work would be required, including:

independent expert adjudication;

broader external genomic validation;

prospectively defined clinically meaningful outcomes;

calibration against an independent target;

validated operating thresholds;

workflow and human-factors evaluation;

prospective safety and clinical-utility assessment.

📌 Current Research Interpretation

The strongest supported conclusion from the current work is deliberately narrow:

ClinVar metadata contain limited but measurable information about future knowledge-record instability. GES can summarize this information as an auditable relative evidence-uncertainty signal, but it is not a calibrated clinical probability and is not consistently superior to simpler metadata baselines.

The RAG experiment further suggests that evidence-stability signals may have their clearest downstream role in uncertainty-aware response policy rather than generic answer-quality improvement.

📖 Publication Status

This repository supports ongoing research on genomic evidence reliability, temporal validation, and evidence-aware biomedical AI.

A revised chapter on GES is currently undergoing the editorial-review process for the edited volume Artificial Intelligence in Therapeutic Innovation.

Publication status should not be interpreted as final acceptance until a formal acceptance decision is issued.

👤 Author

Sanghati Basu
MS Healthcare Informatics, University of Illinois Springfield
ORCID: 0009-0001-2437-773X
Email: sbasu23@uis.edu

Research interests: trustworthy biomedical AI, clinical/biomedical informatics, evidence reliability, interoperability, and uncertainty-aware AI systems.

📄 License

This repository is released under the MIT License.

📌 Citation

A versioned archival citation / DOI will be added when the repository is formally released and archived.

Until then, please cite the repository URL and the specific commit or release used for reproducibility.

⭐ Collaboration

Questions, reproducibility feedback, and research collaboration are welcome.

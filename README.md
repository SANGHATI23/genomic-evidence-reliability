# genomic-evidence-reliability
Quantifies how reliable genomic variant evidence remains over time using ClinVar data, interpretable metadata signals, weak supervision, and a probabilistic Evidence Stability Model. Built for trustworthy biomedical AI, precision oncology, evidence drift analysis, and clinical decision-support reliability research.
# 🧬 Genomic Evidence Stability

A probabilistic framework to quantify how reliable genomic evidence is in biomedical AI systems using ClinVar data.

---

## 🔬 Motivation

AI systems in precision oncology and clinical decision support often assume that genomic knowledge is **static and reliable**.

In reality:

- Variant interpretations evolve over time  
- Conflicting submissions are common  
- Evidence strength varies across genes and contexts  

This creates a hidden risk:

> **AI systems may rely on unstable or conflicting evidence without knowing it.**

---

## ⚠️ Core Concept: Evidence Stability

This project introduces a measurable concept:

> **Evidence Stability = Probability that a genomic variant’s current evidence is reliable**

Instead of treating annotations as ground truth, we model:

- uncertainty  
- disagreement  
- evidence quality  

---

## ⚙️ Methodology

### 📊 Data Source
- ClinVar variant_summary (NCBI)
- Genes:
  - BRCA1
  - BRCA2
  - EGFR
  - MLH1

---

### 🧠 Feature Engineering

We derive interpretable signals:

- **Recency** → how recently the variant was evaluated  
- **Submitter count** → proxy for consensus  
- **Review confidence** → expert vs weak review  
- **Conflict flag** → disagreement in classification  

---

### 🤖 Model

We estimate:

\[
P(\text{stable})
\]

Using an interpretable probabilistic model (logistic regression with weak supervision).

Output:

- `P(stable)` → evidence reliability score  
- `instability_risk = 1 - P(stable)`  

---

## 📈 Key Findings

### 1. Bimodal Stability Structure
- Variants cluster into:
  - **Highly stable**
  - **Highly unstable**
- Few intermediate cases

---

### 2. Conflict Collapses Stability
- Conflicting variants → near-zero stability  
- Strong signal of unreliable evidence  

---

### 3. Review Confidence Drives Stability
- Low review → unstable  
- Expert-reviewed → near-certain stability  

---

### 4. Gene-Level Differences
- MLH1 shows higher average stability  
- EGFR shows lower stability  
- Evidence reliability is not uniform across genes  

---

## 📊 Outputs

- Variant-level stability scores  
- Risk stratification:
  - High Risk
  - Moderate Risk
  - Low Risk  
- Gene-level summaries  
- Case-level unstable variants  

---

## 🧪 Reproducibility

### Run the full pipeline:

1. Open:notebooks/01_evidence_drift_full_pipeline.ipynb
2. 
2. Download ClinVar:
https://ftp.ncbi.nlm.nih.gov/pub/clinvar/tab_delimited/

3. Run all cells

---

## 📁 Repository Structure
notebooks/ → full pipeline
outputs/ → figures + tables
exports/ → processed datasets
paper/ → manuscript drafts

---

## 🚀 Research Impact

This work reframes biomedical AI from:

❌ static knowledge assumption  
→  
✅ **dynamic evidence reliability modeling**

Applications:

- Clinical decision support safety  
- AI confidence calibration  
- Genomic knowledge monitoring  
- Trustworthy AI in healthcare  

---

## ⚠️ Limitations

- Uses proxy labels (weak supervision)  
- Does not predict clinical outcomes  
- Not a clinical decision tool  

---

## 📌 Status

Research prototype for:

- Springer AITI submission  
- PhD research portfolio  
- Trustworthy biomedical AI exploration  

---

## 👤 Author

Deb M  
MS Healthcare Informatics (USA)  
Focus: Reliable & trustworthy clinical AI systems

---

## ⭐ If you find this useful

Consider starring the repo or connecting for collaboration.
3. requirements.txt
pandas
numpy
matplotlib
scikit-learn

4. .gitignore
5. # Large/raw data
data/raw/*
*.gz
*.zip

# Optional processed large files
data/processed/*
exports/final_evidence_stability_dataset.csv

# Keep important result tables
!outputs/tables/*.csv
!exports/gene_stability_summary.csv
!exports/risk_group_counts.csv
!exports/top_unstable_variants.csv

# Figures allowed
!outputs/figures/*.png
!figures/*.png

# Python
__pycache__/
*.pyc
*.pyo
*.pyd

# Jupyter
.ipynb_checkpoints/

# Environment
.venv/
venv/
env/
.env

# OS files
.DS_Store
Thumbs.db

# Logs
*.log

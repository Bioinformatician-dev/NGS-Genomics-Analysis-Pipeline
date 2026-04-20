# Pull Request: Add NGS Variant Calling + NCBI Real Data Pipeline Skills

## 📋 Summary

This PR adds three new bioinformatics agent skills to the repository, covering
end-to-end NGS variant calling workflows — from simulated demo data to real-world
NCBI SRA datasets. All skills include detailed SKILL.md documentation, QC rubrics,
and working Google Colab notebooks.

---

## 🆕 New Skills Added

### 1. `skills/ngs-variant-calling/`
**End-to-end NGS variant calling pipeline (simulated data)**

- Full pipeline: FastQC → Trimmomatic → BWA-MEM → Picard → GATK → Annotation
- Working Google Colab notebook with 13 steps
- Simulated FASTQ data generator for demo purposes
- QC dashboard and variant analysis dashboard (Matplotlib)
- Follows GATK Best Practices throughout

**Files added:**
```
skills/ngs-variant-calling/
├── SKILL.md                    ← Full documentation + rubric
└── NGS_Pipeline_Colab.ipynb   ← Working Colab notebook
```

---

### 2. `skills/ncbi-real-data-pipeline/`
**Real-world pipeline using live NCBI SRA data (SRR1518253)**

- Downloads real human WES data from NCBI SRA using `fasterq-dump`
- Processes HG00096 (1000 Genomes Project, British male)
- Uses hg38 chr22 reference genome (fast, ~30-45 min on Colab free tier)
- Produces real alignment stats, real variant calls, real QC metrics
- All tools auto-installed in Step 0 — zero local setup required
- Results auto-packaged as downloadable ZIP in final step

**Files added:**
```
skills/ncbi-real-data-pipeline/
├── SKILL.md                              ← Full documentation + config guide
└── NCBI_RealData_Pipeline_v2.ipynb      ← Working Colab notebook
```

---

### 3. `skills/qc-rubric-standards/`
**Standardized QC rubric framework for all NGS pipelines**

- Master rubric covering all 8 pipeline stages
- Metric thresholds with Pass / Review / Fail tiers
- GATK hard filter expressions for SNPs and INDELs
- Variant classification framework (ACMG-inspired tiers)
- Python helper function `check_qc_rubric()` for automated evaluation
- Blank rubric scoring template for sample documentation

**Files added:**
```
skills/qc-rubric-standards/
└── SKILL.md      ← Master rubric with all thresholds + Python helper
```

---

## 📦 Repository-Level Changes

### `requirements.txt` (new)
Added a consolidated Python `requirements.txt` covering all pipeline dependencies:
- pandas, numpy, scipy, matplotlib, seaborn, plotly
- biopython, pysam, pyvcf3
- snakemake, jupyter, tqdm, click

### `README.md` (updated)
Added new skills section listing all three new skills with descriptions.

---

## ✅ Testing

All notebooks tested on:
- **Google Colab free tier** (2 vCPU, 12 GB RAM, T4 GPU optional)
- **Python 3.10** with all dependencies from `requirements.txt`
- **Ubuntu 22.04** (Colab base image)

| Notebook | Status | Runtime |
|----------|--------|---------|
| NGS_Pipeline_Colab.ipynb | ✅ All cells pass | ~8 min |
| NCBI_RealData_Pipeline_v2.ipynb | ✅ All cells pass | ~35 min |

---

## 🔬 Scientific Validity

All QC thresholds and variant filter expressions are derived from:
- GATK Best Practices (Broad Institute)
- Illumina sequencing quality metric guidelines
- ACMG variant classification framework
- Published benchmarks from the 1000 Genomes and GIAB projects

---

## 🗂️ Updated Repository Structure

```
bioinformatics-agent-skills/
│
├── README.md                              ← Updated
├── requirements.txt                       ← NEW
│
└── skills/
    ├── ngs-variant-calling/               ← NEW
    │   ├── SKILL.md
    │   └── NGS_Pipeline_Colab.ipynb
    │
    ├── ncbi-real-data-pipeline/           ← NEW
    │   ├── SKILL.md
    │   └── NCBI_RealData_Pipeline_v2.ipynb
    │
    └── qc-rubric-standards/               ← NEW
        └── SKILL.md
```

---

## 💬 Notes for Reviewers

- The NCBI pipeline uses `fasterq-dump -X 500000` to limit reads for Colab.
  Removing this flag runs the full dataset on HPC/cloud.
- All tool installations are handled programmatically in notebook Step 0.
  No manual setup required by the user.
- QC thresholds in `qc-rubric-standards/SKILL.md` are intentionally tiered
  (Excellent / Pass / Review / Fail) to support different use cases.

---

*Submitted by: Salma Hafeez — Computational Biology & Genomics Analyst*
*LinkedIn: https://www.linkedin.com/in/salma-hafeez/*

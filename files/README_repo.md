# 🧬 Bioinformatics Agent Skills

<div align="center">

![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=for-the-badge&logo=jupyter&logoColor=white)
![Google Colab](https://img.shields.io/badge/Google_Colab-Ready-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white)
![GATK](https://img.shields.io/badge/GATK-4.3-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

**A curated collection of bioinformatics agent skills for AI-assisted genomics workflows.
Each skill includes detailed documentation, QC rubrics, working code, and Colab notebooks.**

</div>

---

## 📚 Available Skills

### 🧬 NGS & Variant Calling

| Skill | Description | Notebook | Difficulty |
|-------|-------------|----------|-----------|
| [ngs-variant-calling](skills/ngs-variant-calling/) | End-to-end NGS pipeline with simulated data | ✅ Colab | Intermediate |
| [ncbi-real-data-pipeline](skills/ncbi-real-data-pipeline/) | Full pipeline on real NCBI SRA data (SRR1518253) | ✅ Colab | Advanced |
| [qc-rubric-standards](skills/qc-rubric-standards/) | Master QC rubric framework for all NGS pipelines | 📄 Reference | All levels |

---

## 🚀 Quick Start

### Run any notebook in Google Colab

1. Click a notebook link in the table above
2. Open in [Google Colab](https://colab.research.google.com)
3. Click **Runtime → Run All**
4. Results download automatically as ZIP

### Local installation

```bash
git clone https://github.com/Bioinformatician-dev/bioinformatics-agent-skills.git
cd bioinformatics-agent-skills
pip install -r requirements.txt
```

---

## 🗂️ Repository Structure

```
bioinformatics-agent-skills/
│
├── README.md
├── requirements.txt
│
└── skills/
    ├── ngs-variant-calling/
    │   ├── SKILL.md                    ← Documentation + QC rubric
    │   └── NGS_Pipeline_Colab.ipynb   ← Colab notebook
    │
    ├── ncbi-real-data-pipeline/
    │   ├── SKILL.md                              ← Documentation + config
    │   └── NCBI_RealData_Pipeline_v2.ipynb      ← Real data Colab notebook
    │
    └── qc-rubric-standards/
        └── SKILL.md                    ← Master rubric reference
```

---

## 🛠️ Core Tools Covered

`FastQC` · `Trimmomatic` · `BWA-MEM` · `SAMtools` · `Picard` · `GATK 4.3`
`BCFtools` · `SRA Toolkit` · `ANNOVAR` · `Snakemake` · `Python/pandas/matplotlib`

---

## 📐 QC Standards

All pipelines follow evidence-based QC rubrics. Key thresholds:

| Stage | Metric | Pass Threshold |
|-------|--------|---------------|
| Raw QC | Mean Phred Score | ≥ 28 |
| Raw QC | Q30 % | ≥ 80% |
| Alignment | Mapping Rate | ≥ 90% |
| Alignment | Duplicate Rate | < 25% |
| Coverage | Mean Coverage | ≥ 20x |
| Variants | QUAL Score | ≥ 30 |

Full rubric → [skills/qc-rubric-standards/SKILL.md](skills/qc-rubric-standards/SKILL.md)

---

## 🤝 Contributing

1. Fork this repository
2. Create a branch: `git checkout -b feature/your-skill-name`
3. Add your skill under `skills/your-skill-name/`
4. Include a `SKILL.md` following the template in existing skills
5. Submit a Pull Request

---

## 👩‍🔬 Maintainer

**Salma Hafeez** — Computational Biology & Genomics Analyst

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat&logo=linkedin)](https://www.linkedin.com/in/salma-hafeez/)

---

## 📜 License

MIT License — see [LICENSE](LICENSE) for details.

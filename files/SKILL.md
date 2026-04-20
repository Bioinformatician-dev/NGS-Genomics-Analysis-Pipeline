# 🧬 NGS Variant Calling Skill

## Overview

This skill provides an end-to-end Next Generation Sequencing (NGS) variant calling
pipeline, covering every stage from raw FASTQ quality assessment to annotated variant
reports. It follows GATK Best Practices and is designed for both simulated demo data
and real-world WES/WGS datasets.

---

## Pipeline Stages

```
Raw FASTQ
    │
    ▼
Quality Control     →  FastQC / MultiQC
    │
    ▼
Read Trimming       →  Trimmomatic
    │
    ▼
Sequence Alignment  →  BWA-MEM (hg38)
    │
    ▼
Post-Processing     →  SAMtools + Picard MarkDuplicates
    │
    ▼
Variant Calling     →  GATK HaplotypeCaller (gVCF mode)
    │
    ▼
Variant Filtering   →  GATK VariantFiltration (hard filters)
    │
    ▼
Annotation          →  BCFtools + ANNOVAR / Python
    │
    ▼
Visualization       →  Matplotlib dashboards
```

---

## Tools Required

| Tool | Version | Purpose |
|------|---------|---------|
| FastQC | 0.11.9+ | Per-read quality assessment |
| MultiQC | 1.12+ | Multi-sample QC aggregation |
| Trimmomatic | 0.39 | Adapter & quality trimming |
| BWA-MEM | 0.7.17 | Short-read alignment to hg38 |
| SAMtools | 1.15+ | BAM sorting, indexing, stats |
| Picard | 2.27+ | PCR duplicate marking |
| GATK | 4.3.0 | Variant calling & filtering |
| BCFtools | 1.15+ | VCF manipulation |
| Python | 3.8+ | Annotation & visualization |
| pandas | 1.3+ | Tabular data processing |
| matplotlib | 3.5+ | Plot generation |

### Install (Ubuntu/Colab)
```bash
apt-get install -y fastqc trimmomatic bwa samtools bcftools
pip install pandas numpy matplotlib seaborn biopython pysam
wget https://github.com/broadinstitute/picard/releases/download/2.27.5/picard.jar
wget https://github.com/broadinstitute/gatk/releases/download/4.3.0.0/gatk-4.3.0.0.zip
```

---

## QC Rubric & Thresholds

This skill enforces the following analytical rubric at each pipeline stage.
All thresholds are based on GATK Best Practices and published genomics standards.

### Raw Read Quality
| Metric | Pass Threshold | Action if Failed |
|--------|---------------|-----------------|
| Mean Phred Quality | ≥ 28 | Flag sample for review |
| Q30 Percentage | ≥ 80% | Investigate sequencing run |
| GC Content | 40–60% | Check for contamination |
| Adapter Contamination | < 5% | Re-trim aggressively |

### Alignment Quality
| Metric | Pass Threshold | Action if Failed |
|--------|---------------|-----------------|
| Alignment Rate | ≥ 90% | Verify reference genome match |
| Properly Paired | ≥ 85% | Check library prep protocol |
| Duplicate Rate | < 25% | Check library complexity |
| Mean Coverage | ≥ 20x | Consider re-sequencing |

### Variant Calling Quality (SNPs)
| Filter | Expression | Rationale |
|--------|-----------|-----------|
| QD2 | QD < 2.0 | Low quality-by-depth ratio |
| FS60 | FS > 60.0 | Strand bias |
| MQ40 | MQ < 40.0 | Low mapping quality |
| MQRankSum | < -12.5 | Mapping quality rank sum |
| ReadPosRS | < -8.0 | Read position rank sum |

### Variant Calling Quality (INDELs)
| Filter | Expression | Rationale |
|--------|-----------|-----------|
| QD2 | QD < 2.0 | Low quality-by-depth ratio |
| FS200 | FS > 200.0 | Strand bias (INDELs tolerate more) |
| ReadPosRS | < -20.0 | Read position rank sum |

---

## Usage

### Google Colab (No Setup Required)
1. Open `NGS_Pipeline_Colab.ipynb` in Google Colab
2. Click **Runtime → Run All**
3. Results saved to `/results/` directory
4. Download via Step 13 (auto ZIP)

### Local / HPC
```bash
# Clone and navigate
git clone https://github.com/YOUR_USERNAME/bioinformatics-agent-skills.git
cd bioinformatics-agent-skills/skills/ngs-variant-calling

# Edit config at top of notebook then run
jupyter notebook NGS_Pipeline_Colab.ipynb
```

---

## Outputs

| File | Location | Description |
|------|----------|-------------|
| QC Dashboard | `results/plots/01_QC_Dashboard.png` | Per-base quality, GC, Q30 plots |
| Variant Dashboard | `results/plots/02_Variant_Dashboard.png` | SNP/INDEL distributions |
| Alignment Stats | `results/aligned/*_flagstat.txt` | SAMtools flagstat output |
| Dup Metrics | `results/dedup/*_dup_metrics.txt` | Picard duplication report |
| Filtered VCF | `results/variants/*_filtered.vcf.gz` | GATK hard-filtered variants |
| Annotated CSV | `results/annotated/*_variants.csv` | Tidy variant table |

---

## Interview Talking Points

**Pipeline Design:**
> "I built an end-to-end NGS pipeline following GATK Best Practices, covering QC
> through annotation. Snakemake enables reproducibility and parallel execution."

**Rubric Development:**
> "I developed evidence-based QC rubrics at each stage — Q30 ≥ 80%, alignment
> ≥ 90%, duplicates < 25% — to standardize quality gates across samples."

**Variant Filtering:**
> "Applied GATK hard filters: QD ≥ 2, FS ≤ 60, MQ ≥ 40 for SNPs. These balance
> sensitivity and specificity for germline variant discovery."

---

## References

- [GATK Best Practices](https://gatk.broadinstitute.org/hc/en-us/articles/360035535912)
- [BWA-MEM Paper](https://arxiv.org/abs/1303.3997)
- [1000 Genomes Project](https://www.internationalgenome.org/)
- [Picard Tools](https://broadinstitute.github.io/picard/)

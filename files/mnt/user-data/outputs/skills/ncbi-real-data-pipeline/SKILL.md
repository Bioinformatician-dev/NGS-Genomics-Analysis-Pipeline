# 📥 NCBI Real Data Pipeline Skill

## Overview

This skill downloads real human WES data directly from NCBI SRA and runs a complete
end-to-end variant calling pipeline — from raw reads to annotated, filtered variants
with dashboards. No pre-downloaded data required. Designed to run entirely within
Google Colab free tier (~30–45 minutes).

---

## Dataset Details

| Property | Value |
|----------|-------|
| SRA Accession | SRR1518253 |
| Sample | HG00096 (British male) |
| Project | 1000 Genomes Project |
| Data Type | Whole Exome Sequencing (WES) |
| Sequencer | Illumina HiSeq |
| Reference | hg38 chromosome 22 |
| Colab demo size | 500,000 read-pairs (~500 MB) |
| Full dataset size | ~10 GB |

> **Why chr22?** Chromosome 22 is the smallest human autosome (~51 Mb),
> making it ideal for demonstrations. Remove the `-X 500000` flag in Step 2
> and use the full hg38 reference to run on the complete exome.

---

## Pipeline Steps

| Step | Stage | Tool | Output |
|------|-------|------|--------|
| 0 | Install tools | apt-get / pip | All tools ready |
| 1 | Project setup | Python / os | Directory structure |
| 2 | NCBI SRA download | fasterq-dump | FASTQ.gz files |
| 3 | Reference genome | wget + BWA index | hg38 chr22 + indexes |
| 4 | Quality control | FastQC + Python | QC metrics + dashboard |
| 5 | Read trimming | Trimmomatic | Cleaned FASTQ |
| 6 | Sequence alignment | BWA-MEM | Sorted BAM |
| 7 | Mark duplicates | Picard | Dedup BAM |
| 8 | Variant calling | GATK HaplotypeCaller | Raw VCF |
| 9 | Variant filtering | GATK VariantFiltration | Filtered VCF |
| 10 | Parse & annotate | BCFtools + Python | Annotated CSV |
| 11 | Visualization | Matplotlib | Final dashboards |
| 12 | Summary report | Python | Full printed report |
| 13 | Download results | Colab files API | ZIP archive |

---

## Tools Required

```
SRA Toolkit (fasterq-dump)    — NCBI data download
FastQC                         — Quality control
Trimmomatic 0.39               — Adapter trimming
BWA-MEM 0.7.17                 — Sequence alignment
SAMtools 1.15+                 — BAM processing
Picard 2.27.5                  — Duplicate marking
GATK 4.3.0.0                   — Variant calling & filtering
BCFtools 1.15+                 — VCF parsing
Python 3.8+                    — Annotation, visualization, reporting
```

### Installation (auto-runs in Step 0 of notebook)
```bash
# SRA Toolkit
wget https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/current/sratoolkit.current-ubuntu64.tar.gz

# Picard
wget https://github.com/broadinstitute/picard/releases/download/2.27.5/picard.jar

# GATK
wget https://github.com/broadinstitute/gatk/releases/download/4.3.0.0/gatk-4.3.0.0.zip

# System tools
apt-get install -y fastqc trimmomatic bwa samtools bcftools pigz
```

---

## Configuration

Edit these variables at the top of the notebook (Step 1):

```python
PROJECT_DIR  = '/content/ngs_project'   # Working directory
SRA_ID       = 'SRR1518253'             # NCBI SRA accession
SAMPLE_NAME  = 'HG00096_chr22'          # Human-readable label
CHROM        = 'chr22'                  # Target chromosome
MAX_READS    = 500000                   # Reads to download (None = all)
THREADS      = 2                        # CPU threads
MIN_QUAL     = 30                       # Minimum variant QUAL
MIN_DEPTH    = 10                       # Minimum read depth
```

---

## QC Rubric (Real Data Standards)

| Stage | Metric | Pass Threshold |
|-------|--------|---------------|
| Raw QC | Mean Phred Score | ≥ 25 |
| Raw QC | Q30 Percentage | ≥ 60% |
| Raw QC | GC Content | 35–65% |
| Alignment | Mapping Rate | ≥ 85% |
| Alignment | Properly Paired | ≥ 80% |
| Dedup | Duplicate Rate | < 30% |
| Variants | QUAL score | ≥ 30 |
| Variants | Read Depth | ≥ 10x |

> Note: Thresholds for real SRA data are slightly relaxed compared to
> clinical-grade pipelines because public SRA datasets vary in quality.

---

## Outputs

```
results/
├── plots/
│   ├── 01_QC_Dashboard.png         ← Per-base quality, GC, Q30, rubric table
│   └── 02_Variant_Dashboard.png    ← SNP/INDEL pie, QUAL dist, depth, scatter
├── aligned/
│   ├── HG00096_chr22.bam           ← Sorted aligned BAM
│   └── HG00096_chr22_flagstat.txt  ← Alignment statistics
├── dedup/
│   ├── HG00096_chr22_dedup.bam     ← Deduplicated BAM
│   └── HG00096_chr22_dup_metrics.txt
├── variants/
│   ├── HG00096_chr22_raw.vcf.gz    ← Raw variants
│   ├── HG00096_chr22_snps_filtered.vcf.gz
│   └── HG00096_chr22_indels_filtered.vcf.gz
└── annotated/
    └── HG00096_chr22_variants.csv  ← Final tidy variant table
```

---

## Scaling to Full Dataset

```python
# In Step 1 of notebook, change:
MAX_READS = None          # Download all reads (not just 500k)
CHROM     = 'all'         # Use full reference genome

# In Step 3, use full hg38 instead of chr22:
# wget https://hgdownload.soe.ucsc.edu/goldenPath/hg38/bigZips/hg38.fa.gz

# For HPC, replace fasterq-dump with:
# prefetch SRR1518253 && fasterq-dump SRR1518253 --split-files
```

---

## Cloud Deployment Options

| Platform | Best For | Notes |
|----------|----------|-------|
| Google Colab | Demo / Learning | Free tier, chr22 only |
| Google Cloud Life Sciences | GATK pipelines | Native GATK support |
| Terra (Broad Institute) | TCGA/GTEx data | WDL workflow support |
| AWS Genomics CLI | Large cohorts | S3 + Batch integration |
| DNAnexus | Clinical labs | HIPAA compliant |

---

## Interview Talking Points

**On real data:**
> "I ran the pipeline on SRR1518253 — a real human WES sample from the 1000 Genomes
> Project. Using public data proves the pipeline works on genuine sequencing output."

**On reproducibility:**
> "The entire pipeline runs in Google Colab with no local setup. Anyone can reproduce
> the results with just the SRA accession number and this notebook."

**On tool selection:**
> "I followed GATK Best Practices: BWA-MEM for alignment, Picard for duplicate
> marking, HaplotypeCaller in standard mode for germline variant discovery."

---

## References

- [NCBI SRA: SRR1518253](https://www.ncbi.nlm.nih.gov/sra/SRR1518253)
- [1000 Genomes Project](https://www.internationalgenome.org/)
- [GATK Best Practices — Germline SNPs/INDELs](https://gatk.broadinstitute.org)
- [UCSC hg38 Reference](https://hgdownload.soe.ucsc.edu/goldenPath/hg38/)

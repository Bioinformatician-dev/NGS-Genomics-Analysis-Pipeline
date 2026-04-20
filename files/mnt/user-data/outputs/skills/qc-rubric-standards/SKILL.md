# 📐 QC Rubric Standards Skill

## Overview

This skill defines the standardized Quality Control (QC) rubric framework used
across all NGS bioinformatics pipelines in this repository. Rubrics provide
pass/fail criteria at each pipeline stage, ensuring analytical rigor, reproducibility,
and consistency across datasets, samples, and research teams.

---

## What is a Bioinformatics Rubric?

A rubric is a **structured evaluation framework** — a set of measurable criteria
with defined thresholds used to assess whether a pipeline stage meets scientific
and operational standards.

Think of it like a grading rubric, but instead of evaluating writing, you are
evaluating the quality of genomic data and analysis outputs at each step.

---

## Master QC Rubric — All Pipeline Stages

### Stage 1: Raw Read Quality (FastQC)

| Metric | Excellent | Pass | Review | Fail |
|--------|-----------|------|--------|------|
| Mean Phred Score | ≥ 35 | ≥ 28 | 20–27 | < 20 |
| Q30 Percentage | ≥ 90% | ≥ 80% | 60–79% | < 60% |
| GC Content | 45–55% | 40–60% | 35–39% / 61–65% | < 35% or > 65% |
| Adapter Contamination | < 1% | < 5% | 5–10% | > 10% |
| Duplicate Rate (raw) | < 5% | < 15% | 15–25% | > 25% |
| N Base Content | < 0.1% | < 1% | 1–5% | > 5% |

**Actions:**
- **Review:** Flag sample, notify team, proceed with caution
- **Fail:** Do not proceed; investigate sequencing run; consider re-sequencing

---

### Stage 2: Read Trimming (Trimmomatic)

| Metric | Pass | Review | Fail |
|--------|------|--------|------|
| Reads surviving trimming | ≥ 90% | 75–89% | < 75% |
| Paired reads retained | ≥ 85% | 70–84% | < 70% |
| Mean read length post-trim | ≥ 100 bp | 50–99 bp | < 50 bp |

---

### Stage 3: Sequence Alignment (BWA-MEM)

| Metric | Excellent | Pass | Review | Fail |
|--------|-----------|------|--------|------|
| Overall Mapping Rate | ≥ 97% | ≥ 90% | 80–89% | < 80% |
| Properly Paired Reads | ≥ 95% | ≥ 85% | 70–84% | < 70% |
| Singletons | < 1% | < 3% | 3–5% | > 5% |
| Secondary Alignments | < 1% | < 3% | 3–8% | > 8% |

**Diagnosis for low mapping rate:**
- Check reference genome version (hg38 vs hg19)
- Check for sample contamination
- Verify organism of origin

---

### Stage 4: Duplicate Marking (Picard)

| Metric | Excellent | Pass | Review | Fail |
|--------|-----------|------|--------|------|
| Duplicate Rate | < 5% | < 25% | 25–40% | > 40% |
| Estimated Library Size | > 50M | > 10M | 5–10M | < 5M |

**Note:** Higher duplicate rates are acceptable for:
- Low-input libraries
- Targeted amplicon sequencing
- FFPE samples

---

### Stage 5: Coverage (Post-Dedup)

| Application | Minimum Coverage | Recommended | High Confidence |
|-------------|-----------------|-------------|-----------------|
| WGS (germline) | 15x | 30x | 60x+ |
| WES (germline) | 20x | 50x | 100x+ |
| Somatic (tumor) | 30x | 100x | 200x+ |
| CRISPR screen | 300x | 500x | 1000x+ |
| Single-cell | 1x | 5x | 20x+ |

| Metric | Pass | Review | Fail |
|--------|------|--------|------|
| Mean target coverage | ≥ 20x | 10–19x | < 10x |
| % bases ≥ 10x | ≥ 90% | 75–89% | < 75% |
| % bases ≥ 20x | ≥ 80% | 60–79% | < 60% |
| Coverage uniformity | ≥ 0.8 | 0.6–0.79 | < 0.6 |

---

### Stage 6: Variant Calling — SNP Hard Filters (GATK)

| Filter Name | Expression | Rationale |
|-------------|-----------|-----------|
| QD2 | QD < 2.0 | Low quality normalized by depth |
| FS60 | FS > 60.0 | Strand bias (Fisher's exact test) |
| MQ40 | MQ < 40.0 | Low root mean square mapping quality |
| MQRankSum-12.5 | MQRankSum < -12.5 | Mapping quality rank sum test |
| ReadPosRankSum-8 | ReadPosRankSum < -8.0 | Read position rank sum test |
| SOR3 | SOR > 3.0 | Strand odds ratio (optional) |

---

### Stage 7: Variant Calling — INDEL Hard Filters (GATK)

| Filter Name | Expression | Rationale |
|-------------|-----------|-----------|
| QD2 | QD < 2.0 | Low quality by depth |
| FS200 | FS > 200.0 | Strand bias (higher for INDELs) |
| ReadPosRankSum-20 | ReadPosRankSum < -20.0 | Read position bias |
| InbreedingCoeff | InbreedingCoeff < -0.8 | Inbreeding coefficient |

---

### Stage 8: Variant Annotation & Classification

#### Population Frequency Thresholds
| Tier | ExAC / gnomAD AF | Classification |
|------|-----------------|----------------|
| Ultra-rare | < 0.0001 (0.01%) | High priority |
| Rare | < 0.001 (0.1%) | Medium priority |
| Uncommon | < 0.01 (1%) | Low priority |
| Common | ≥ 0.01 (1%) | Typically benign |

#### Functional Impact Scoring
| Score | SIFT | PolyPhen2 | Classification |
|-------|------|-----------|----------------|
| Damaging | < 0.05 | > 0.85 | Pathogenic candidate |
| Borderline | 0.05–0.10 | 0.50–0.85 | Uncertain |
| Tolerated | > 0.10 | < 0.50 | Likely benign |

#### Pathogenicity Composite Score
```python
# Composite score formula used in this pipeline
pathogenicity_score = (
    (1 - SIFT_score) * 0.5 +
    PolyPhen2_score  * 0.5
)

# Classification
if score > 0.7:   classification = 'Pathogenic'
elif score > 0.4: classification = 'Uncertain'
else:             classification = 'Benign'
```

#### Clinical Variant Tiers (ACMG-inspired)
| Tier | Criteria | Action |
|------|----------|--------|
| Tier 1 | ClinVar Pathogenic/Likely Pathogenic | Report immediately |
| Tier 2 | Rare + damaging functional prediction | Prioritize follow-up |
| Tier 3 | Rare + exonic, unknown significance (VUS) | Functional validation |
| Tier 4 | Common or intronic | Deprioritize |

---

## Rubric Review Workflow

```
Sample arrives
      │
      ▼
Stage 1: Raw QC check  ──FAIL──► Hold sample, investigate
      │ PASS
      ▼
Stage 2: Trimming check ──FAIL──► Adjust parameters
      │ PASS
      ▼
Stage 3: Alignment check ──FAIL──► Check reference / contamination
      │ PASS
      ▼
Stage 4: Dedup check ──REVIEW──► Note in report, proceed
      │ PASS
      ▼
Stage 5: Coverage check ──FAIL──► Flag for re-sequencing
      │ PASS
      ▼
Stage 6–7: Variant filter check
      │
      ▼
Stage 8: Annotation & classification
      │
      ▼
Final Report ─► Archive results + rubric scores
```

---

## Rubric Scoring Template

Use this template to document rubric results for each sample:

```markdown
## QC Rubric Report
- Sample:        _______________
- Date:          _______________
- Analyst:       _______________
- Pipeline:      NGS Variant Calling v1.0

| Stage | Metric | Value | Threshold | Status |
|-------|--------|-------|-----------|--------|
| Raw QC | Mean Q | | ≥ 28 | |
| Raw QC | Q30 % | | ≥ 80% | |
| Raw QC | GC % | | 40–60% | |
| Alignment | Mapping rate | | ≥ 90% | |
| Alignment | Duplicate rate | | < 25% | |
| Coverage | Mean coverage | | ≥ 20x | |
| Coverage | % bases ≥ 20x | | ≥ 80% | |
| Variants | SNPs called | | — | |
| Variants | INDELs called | | — | |

Overall Status: [ ] PASS  [ ] REVIEW  [ ] FAIL
Notes: _______________
```

---

## Python Helper: Automated Rubric Checker

```python
def check_qc_rubric(metrics: dict) -> dict:
    """
    Automatically evaluate a sample against the QC rubric.

    Parameters
    ----------
    metrics : dict with keys:
        mean_quality, q30_pct, gc_pct, mapping_rate,
        duplicate_rate, mean_coverage, pct_bases_20x

    Returns
    -------
    dict with per-metric pass/review/fail status
    """
    rubric = {
        'mean_quality':    {'pass': 28,  'review': 20,  'higher_is_better': True},
        'q30_pct':         {'pass': 80,  'review': 60,  'higher_is_better': True},
        'mapping_rate':    {'pass': 90,  'review': 80,  'higher_is_better': True},
        'duplicate_rate':  {'pass': 25,  'review': 40,  'higher_is_better': False},
        'mean_coverage':   {'pass': 20,  'review': 10,  'higher_is_better': True},
        'pct_bases_20x':   {'pass': 80,  'review': 60,  'higher_is_better': True},
    }

    results = {}
    overall = 'PASS'

    for metric, val in metrics.items():
        if metric not in rubric:
            continue
        r = rubric[metric]
        if r['higher_is_better']:
            if val >= r['pass']:   status = '✅ PASS'
            elif val >= r['review']: status = '⚠️  REVIEW'; overall = 'REVIEW'
            else:                  status = '❌ FAIL';   overall = 'FAIL'
        else:
            if val <= r['pass']:   status = '✅ PASS'
            elif val <= r['review']: status = '⚠️  REVIEW'; overall = 'REVIEW'
            else:                  status = '❌ FAIL';   overall = 'FAIL'
        results[metric] = {'value': val, 'status': status}

    results['overall'] = overall
    return results


# Example usage:
sample_metrics = {
    'mean_quality':   31.5,
    'q30_pct':        85.2,
    'mapping_rate':   94.7,
    'duplicate_rate': 12.3,
    'mean_coverage':  42.0,
    'pct_bases_20x':  88.5,
}
report = check_qc_rubric(sample_metrics)
for k, v in report.items():
    if isinstance(v, dict):
        print(f"  {k:20s}: {v['value']:>6.1f}  {v['status']}")
    else:
        print(f"\n  Overall: {v}")
```

---

## References

- [GATK Best Practices — Germline Variant Discovery](https://gatk.broadinstitute.org/hc/en-us/articles/360035535912)
- [Illumina Sequencing Quality Metrics](https://support.illumina.com/bulletins/2014/09/qc-metric-definitions.html)
- [ACMG Variant Classification Guidelines](https://www.acmg.net/ACMG/Medical-Genetics-Practice-Resources/Practice-Guidelines.aspx)
- [gnomAD Population Frequencies](https://gnomad.broadinstitute.org/)
- [ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/)

# Methylation Tissue Classifier

A Python pipeline for tissue-specific DNA methylation analysis using a curated 25-gene panel across 6 cancer types from TCGA. Demonstrates methylation-based tissue-of-origin classification — the epigenomic foundation of liquid biopsy diagnostics.

## Biological motivation

DNA methylation patterns are highly tissue-specific and preserved in cancer cells, making them powerful biomarkers for tissue-of-origin classification. This project builds a literature-curated methylation panel targeting genes known to show tissue-specific silencing — directly relevant to liquid biopsy and cancer diagnostics applications (cf. Grail Galleri, Foundation Medicine).

## Gene panel (25 genes)

| Category | Genes | Tissue bias |
|----------|-------|-------------|
| Tumor suppressors | APC, MLH1, BRCA1, SFRP1, CDH1, PTEN, RB1, RARB | Pan-cancer / tissue-specific |
| Cell cycle | CDKN2A | Pan-cancer |
| Tissue-specific TFs | HOXA5, CDX2, PAX6, GATA4, NKX2-1 | Blood, Colon, Brain, GI, Lung |
| Cancer biomarkers | GSTP1, MGMT, SEPT9, SHOX2, VIM | Prostate, Brain, Colon, Lung |
| Immune checkpoint | CD274 | Pan-cancer |
| DNA repair | PARP1 | Brain |
| Signaling | KRAS | Pan-cancer |
| Metabolism | PCSK9 | Pan-cancer |
| Hematopoietic TFs | RUNX1, TET2 | Blood |

## Key findings

### Statistical analysis (whole-gene methylation)
- **13/25 genes** significantly differentially methylated across cancer types (Kruskal-Wallis, FDR<0.05)
- **HOXA5** largest differential (Δ=0.405, LAML vs GBM)
- **GSTP1** correctly identifies prostate — validates panel against literature
- **PCSK9** significant (FDR=0.015) — novel metabolic gene methylation finding

### Promoter-specific analysis (TSS±2kb)
- 24/25 genes have promoter CpGs on the 450k array (CDKN2A missing)
- **10/24 genes better discriminated by promoter vs whole-gene methylation**
- **PARP1:** not significant whole-gene (p=0.403) → significant at promoter (p=0.005)
- **RB1:** 0.452 → 0.026 at promoter level
- **GSTP1:** crosses significance threshold at promoter (0.051 → 0.004)
- **MGMT:** better at whole-gene — signal is in gene body, not promoter

### Pathway enrichment
| Pathway | Genes | KW p-value | Max delta | Significant |
|---------|-------|------------|-----------|-------------|
| Immune Regulation | CD274, VIM | 0.015 | 0.187 | Yes |
| Metabolism | PCSK9, KRAS | 0.157 | 0.185 | No |
| Tissue Identity TFs | HOXA5, CDX2, PAX6, GATA4, NKX2-1 | 0.294 | 0.113 | No |
| DNA Damage Response | PARP1, BRCA1, MLH1, RB1 | 0.703 | 0.060 | No |
| Cell Cycle Control | CDKN2A, RB1, PTEN | 0.981 | 0.013 | No |

### Top discriminating genes per cancer type
| Cancer | Best marker | Delta vs others |
|--------|-------------|-----------------|
| GBM | CDH1 | +0.128 |
| BRCA | CDX2 | +0.128 |
| COAD | GATA4 | +0.140 |
| LUAD | CDX2 | +0.076 |
| PRAD | RARB | +0.219 |
| LAML | HOXA5 | +0.316 |

### Random forest classifier (LOO-CV, n=5 per type)
- **60% overall accuracy**
- **COAD: 5/5 correct** — colon most epigenetically distinct
- **LAML: 5/5 correct** — blood separates perfectly (confirmed by PCA)
- Top features: CD274, CDH1, HOXA5, TET2, VIM

### PCA
- PC1 (25%) separates LAML from solid tumors
- PC2 (16.3%) separates PRAD from others
- 53.4% variance explained in 3 PCs

### Gene correlations (notable)
- PAX6 ↔ GATA4 (r=0.74) — developmental TF co-regulation
- RUNX1 ↔ TET2 (r=0.62) — blood epigenetic axis
- CDH1 ↔ PCSK9 (r=0.72) — novel co-methylation finding

## Figures

### Promoter methylation heatmap (TSS±2kb)
![Promoter heatmap](results/plots/promoter_heatmap.png)

### Whole-gene methylation heatmap
![Heatmap](results/plots/heatmap.png)

### PCA — cancer type separation
![PCA](results/plots/pca.png)

### Random forest classifier
![RF](results/plots/random_forest.png)

### Pathway enrichment analysis
![Pathway](results/plots/pathway_enrichment.png)

### Top discriminating genes per cancer type
![Discriminating](results/plots/discriminating_genes.png)

### Gene-gene correlation heatmap
![Correlation](results/plots/gene_correlation.png)

### Tissue specificity dot plot
![Dot plot](results/plots/dot_plot.png)

## Notebooks

| Notebook | Description |
|----------|-------------|
| 01_ccle_methylation_panel.ipynb | Complete pipeline: download, QC, gene panel, all analyses |

## Installation

```bash
git clone https://github.com/bmurnyak/methylation-tissue-classifier.git
cd methylation-tissue-classifier
conda activate glioma-meth
pip install scikit-learn jinja2
```

## Data sources
- TCGA 450k methylation + RNA-seq: [GDC portal](https://portal.gdc.cancer.gov)
- 450k manifest: Illumina HumanMethylation450 v1.2 (download separately)

## Next steps
- [ ] Fix methylation-expression anti-correlation (gene name alignment)
- [ ] Scale to 20+ samples per cancer type
- [ ] Add more cancer types (STAD, UCEC, KIRC)
- [ ] Validate PARP1 promoter finding against glioma pipeline results

## Author

**Balazs Murnyak, PhD** — Molecular Biologist and Genomics Scientist
University of Utah
[LinkedIn](https://www.linkedin.com/in/balazs-murnyak-56a45a100/) | [Google Scholar](https://scholar.google.com/citations?user=dFfVIEAAAAJ)

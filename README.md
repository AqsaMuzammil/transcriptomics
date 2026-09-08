# Sex-Dependent Transcriptomic Analysis of Nep1 and Nepl15 in *Drosophila melanogaster*

RNA-seq analysis code from my MS thesis:

> **Sex-Dependent Transcriptomic Analysis of Nep1 and Nepl15 and Functional
> Analysis of Muscle-Related Genes in Drosophila melanogaster**
> Aqsa Muzammil, MS Thesis, Department of Biology, New Mexico State
> University, July 2026. Advisor: Dr. Jennifer Curtiss.

## Overview

Neprilysins are peptide-regulating enzymes that help control extracellular
signaling. In *Drosophila melanogaster*, **Nep1** is predicted to be an
active neprilysin, while **Nepl15** is a neprilysin-like protein previously
linked to lipid and glycogen storage. This project uses RNA-seq to
characterize gene expression changes in `Nep1` and `Nepl15` mutants,
comparing males and females, to test whether these genes act in a
sex-dependent manner and whether the transcriptomic changes are consistent
with a muscle-structure phenotype.

Full methods, functional/behavioral validation (qPCR, climbing assays,
locomotor activity monitoring, sarcomere length measurements), and
biological interpretation are in the thesis; this repo covers the RNA-seq /
DESeq2 side of the analysis.

## Repository contents

```
scripts/
  01_sex_genotype_interaction_analysis.R   # Single model with sex:genotype interaction
  02_four_contrast_deseq2_pipeline.R       # Four pairwise mutant-vs-w1118 contrasts, split by sex
data/
  README.md                                # Expected input file formats
results/                                   # DESeq2 output CSVs land here when scripts are run
```

### `scripts/01_sex_genotype_interaction_analysis.R`

Fits one DESeq2 model with design `~ geno_simple + sex + geno_simple:sex`
and extracts:

- the baseline sex effect (male vs female) in the `w1118` control
- the sex effect within `Nep1` and within `Nepl15` (baseline + interaction term)
- genotype-specific sex bias, i.e. the interaction term alone (does the M–F
  gap in the mutant differ from the M–F gap in `w1118`?)
- genotype-specific sex-biased gene sets via a set-difference method (DE
  for sex in the mutant but not in `w1118`)
- genes that **lost** sex bias in the mutants relative to `w1118`

### `scripts/02_four_contrast_deseq2_pipeline.R`

Runs four independent DESeq2 models, each comparing one mutant to `w1118`
within a single sex:

1. Nepl15 females vs w1118 females
2. Nep1 females vs w1118 females
3. Nep1 males vs w1118 males
4. Nepl15 males vs w1118 males

For each contrast it writes an `_ALL.csv` (every tested gene) and a
`_SIG_padj<0.05_absLFC>=1.csv` (significant subset) file.

## Requirements

- R (>= 4.2 recommended)
- Bioconductor: `DESeq2`
- CRAN: `dplyr`, `readr`, `tibble`

```r
install.packages(c("dplyr", "readr", "tibble"))
if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager")
BiocManager::install("DESeq2")
```

## Input data

Both scripts expect, in the working directory set at the top of each script:

- `sampleTable.csv` — one row per RNA-seq sample, with at least `sample`,
  `sex`, and `genotype` columns
- `feature_count_table.csv` — a gene x sample raw count matrix (first
  column = gene IDs, remaining columns = sample IDs matching `sample` in
  `sampleTable.csv`)

See `data/README.md` for the expected column layout. Raw counts are not
included in this repo — see that file for how to obtain them.

## Running

Update the `setwd(...)` path near the top of each script to your local data
directory, then from R:

```r
source("scripts/01_sex_genotype_interaction_analysis.R")
source("scripts/02_four_contrast_deseq2_pipeline.R")
```

Output CSVs are written to the working directory; move or symlink that to
`results/` to keep things tidy.

## Citation

If you use this code, please cite the thesis:

```
Muzammil, A. (2026). Sex-Dependent Transcriptomic Analysis of Nep1 and
Nepl15 and Functional Analysis of Muscle-Related Genes in Drosophila
melanogaster. MS Thesis, New Mexico State University.
```

## License

MIT — see [LICENSE](LICENSE).

# recomb_vus_prioritization
A Multimodal Network-Based Approach for Disease-Specific Prioritization of Rare Variants of Uncertain Significance test page

## Overview

MM Vus Prioritization integrates ANNOVAR variant annotations with protein-protein interaction networks to predict variant pathogenicity. The framework uses a cross-modal attention mechanism to combine genomic features with network topology information.

**Key components:**
- Modal 1: ANNOVAR prediction scores + calibrated classifier
- Modal 2: STRING PPI network + RWR/PPR propagation + GCN encoder
- Cross-modal attention for adaptive information fusion
- Gene-level ranking for variant prioritization

## Quick Start

<img width="635" height="299" alt="image" src="https://github.com/user-attachments/assets/90b59dba-e0e4-4a00-848f-6ea9bdfb183a" />

After installing the dependencies (`pip install -r requirements.txt`),  
open the `.ipynb` files in this repository with Jupyter Notebook or JupyterLab  
and run all cells from top to bottom in numerical filename order.

## Input Format

ANNOVAR-annotated CSV file with:
- Variant info: `Chr`, `Start`, `End`, `Ref`, `Alt`
- Gene: `Gene.refGene`
- 19 prediction scores: `SIFT4G_score`, `LRT_score`, `MutationTasser_score`, `MutationAssessor_score`, `FATHMM_score`, `PROVEAN_score`, `CADD_phred`, `integrated_fitCons_score`, `LINSIGHT`, `GERP++_RS`, `phyloP100way_vertebrate`, `phyloP470way_mammalian`, `phastCons100way_vertebrate`, `phastCons470way_mammalian`, `SiPhy_29way_logOdds`, `bStatistic`, `gnomad41_genome`, `gnomad41_exome`, `AF_patient`(=inner_AF)
- (For training) Label: `ClinSIG`

- ***`AF_internal`: Internal cohort allele frequency.***
  In the sample dataset, `AF_internal` is set to missing (NA) because it depends on the user’s own cohort VCF/BGEN and cannot be computed from ClinVar or external reference files alone.  
  When applying MM-PathPredict to your own data, you should calculate `AF_internal` from your cohort genotypes and merge it into the input table if needed.

## Output

The model produces:
- `all_predictions.csv` - All variants ranked by pathogenicity score
- `seed_gene_predictions.csv` - Variants in known MM genes
- `novel_gene_predictions.csv` - Candidate disease gene variants
- `high_risk_predictions.csv` - High-confidence predictions (probability > 0.7)
- Visualization of score distributions

Example output:
```
Top 20 Novel Gene Variants:
Gene      CADD  ranking_score  pathogenic_proba
CPNE9     31    20.29          0.997
METTL17   38    16.36          0.989
SLK       49    8.93           0.901
...
```

## Requirements

- Python 3.8+
- PyTorch 2.0+
- See `requirements.txt` for full dependencies

## Citation

Citation information is not yet available.  
This is a prototype repository prepared for a potential RECOMB 2026 submission.

## License

This project is released under the MIT License (see the `LICENSE` file).

Note: At this stage, the repository is an experimental prototype prepared for a potential RECOMB 2026 submission.  
APIs, file formats, and implementation details are subject to change without notice.

## Contact

- GitHub Issues: [Issues](https://github.com/jeungmin-LEE/recomb_vus_prioritization/issues)

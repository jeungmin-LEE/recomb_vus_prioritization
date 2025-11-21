# recomb_vus_prioritization
A Multimodal Network-Based Approach for Disease-Specific Prioritization of Rare Variants of Uncertain Significance test page

## Overview

MM-PathPredict integrates ANNOVAR variant annotations with protein-protein interaction networks to predict variant pathogenicity. The framework uses a cross-modal attention mechanism to combine genomic features with network topology information.

**Key components:**
- Modal 1: 19 ANNOVAR prediction scores + calibrated classifier
- Modal 2: STRING PPI network + RWR/PPR propagation + GCN encoder
- Cross-modal attention for adaptive information fusion
- Gene-level ranking for variant prioritization

## Quick Start

### Option A: Pre-trained Model (Recommended)

```bash
pip install -r requirements.txt

# Download pre-trained model from GitHub Releases
# - pretrained_model.pkl
# - network_data.pkl

python predict_with_pretrained.py --vus_file your_data.csv
```

Results saved to `prediction_results/`

### Option B: Train Your Own Model

```bash
# Install dependencies
pip install -r requirements.txt

# Download STRING database
cd data/
wget https://stringdb-downloads.org/download/protein.links.v12.0/9606.protein.links.v12.0.txt.gz
wget https://stringdb-downloads.org/download/protein.aliases.v12.0/9606.protein.aliases.v12.0.txt.gz
gunzip *.gz

# Train
python main_integrated.py \
  --annovar_file data/your_training_data.csv \
  --string_file data/9606.protein.links.v12.0.txt \
  --alias_file data/9606.protein.aliases.v12.0.txt \
  --mm_genes_file data/mm_genes.txt

# Predict
python test_model.py \
  --test_file your_vus_data.csv \
  --model_file results/trained_model.pkl \
  --network_file results/network_data.pkl
```

## Input Format

ANNOVAR-annotated CSV file with:
- Variant info: `Chr`, `Start`, `End`, `Ref`, `Alt`
- Gene: `Gene.refGene`
- 19 prediction scores: `SIFT4G_score`, `LRT_score`, `MutationTasser_score`, `MutationAssessor_score`, `FATHMM_score`, `PROVEAN_score`, `CADD_phred`, `integrated_fitCons_score`, `LINSIGHT`, `GERP++_RS`, `phyloP100way_vertebrate`, `phyloP470way_mammalian`, `phastCons100way_vertebrate`, `phastCons470way_mammalian`, `SiPhy_29way_logOdds`, `bStatistic`, `gnomad41_genome`, `gnomad41_exome`, `AF`
- (For training) Label: `ClinVar_SIG`

See `data/README.md` for details.

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

## Documentation

- `QUICK_START.md` - Detailed usage guide
- `data/README.md` - Data preparation
- `examples/` - Usage examples
- `examples/example_outputs/` - Example results

## Citation

```bibtex
@article{yourname2024,
  title={Multimodal Deep Learning for Multiple Myeloma Pathogenic Variant Prediction},
  author={Your Name},
  journal={Journal/Conference},
  year={2024}
}
```

## License

MIT License - see LICENSE file

## Contact

- GitHub Issues: [Issues](https://github.com/yourusername/mm-pathpredict/issues)
- Email: your.email@example.com

# 🚀 Quick Start Guide

Get started with MM-PathPredict in 5 minutes!

## 📋 Prerequisites

```bash
# Install dependencies
pip install -r requirements.txt

# Download STRING PPI data (if training from scratch)
cd data/
wget https://stringdb-downloads.org/download/protein.links.v12.0/9606.protein.links.v12.0.txt.gz
wget https://stringdb-downloads.org/download/protein.aliases.v12.0/9606.protein.aliases.v12.0.txt.gz
gunzip *.gz
```

---

## 🎯 Two Ways to Use MM-PathPredict

### Option A: Use Pre-trained Model (⚡ Fastest - Recommended)
**Best for**: Immediately predicting pathogenicity on your VUS data

### Option B: Train Your Own Model (🔬 Educational)
**Best for**: Understanding the framework, custom training

---

## ⚡ Option A: Pre-trained Model (5 minutes)

### Step 1: Download Pre-trained Model

Download these files from [GitHub Releases](https://github.com/yourusername/mm-pathpredict/releases):
- `pretrained_model.pkl` (~50MB) - Trained model weights
- `network_data.pkl` (~30MB) - PPI network data

Place them in the project root directory.

### Step 2: Prepare Your VUS Data

Your VUS file should be **ANNOVAR-annotated CSV** with:
- Variant info: `Chr`, `Start`, `End`, `Ref`, `Alt`
- Gene name: `Gene.refGene`
- 19 prediction scores: `SIFT4G_score`, `CADD_phred`, `GERP++_RS`, etc.

**Example format**:
```csv
Chr,Start,End,Ref,Alt,Gene.refGene,CADD_phred,SIFT4G_score,...
1,12345,12345,A,T,KRAS,35,0.05,...
2,67890,67890,G,C,TP53,42,0.02,...
```

See `data/README.md` for complete format specification.

### Step 3: Run Prediction

```bash
python predict_with_pretrained.py --vus_file your_vus_data.csv
```

**Optional arguments**:
```bash
# Custom MM genes list
python predict_with_pretrained.py \
  --vus_file your_vus_data.csv \
  --mm_genes data/custom_genes.txt

# Custom output directory
python predict_with_pretrained.py \
  --vus_file your_vus_data.csv \
  --output_dir my_results

# Show top N variants
python predict_with_pretrained.py \
  --vus_file your_vus_data.csv \
  --show_top 50
```

### Step 4: View Results

Results are saved in `prediction_results/` (or your custom directory):

```
prediction_results/
├── all_predictions.csv              # All variants ranked
├── seed_gene_predictions.csv        # Known MM genes
├── novel_gene_predictions.csv       # Candidate genes
├── high_risk_predictions.csv        # High-risk (prob > 0.7)
└── test_predictions_distribution.png # Visualization
```

**Example output**:
```
Top 20 Novel Gene Variants:
Gene        CADD_phred  ranking_score  pathogenic_proba
CPNE9       31          20.29          0.997
METTL17     38          16.36          0.989
SLK         49          8.93           0.901
...
```

See `examples/example_outputs/example_output.txt` for full output example.

---

## 🔬 Option B: Train Your Own Model (30-60 minutes)

### Step 1: Download Sample Training Data

Option 1: Use our sample data (~3MB)
```bash
# Download from GitHub Releases
wget https://github.com/yourusername/mm-pathpredict/releases/download/v1.0.0/sample_training_data.csv
```

Option 2: Prepare your own data
- ClinVar or other labeled data (Pathogenic/Benign)
- ANNOVAR-annotated
- At least 500 variants (balanced P/B)

### Step 2: Prepare STRING PPI Data

```bash
cd data/
# Download STRING database
wget https://stringdb-downloads.org/download/protein.links.v12.0/9606.protein.links.v12.0.txt.gz
wget https://stringdb-downloads.org/download/protein.aliases.v12.0/9606.protein.aliases.v12.0.txt.gz
gunzip *.gz
cd ..
```

### Step 3: Run Training

```bash
python main_integrated.py \
  --annovar_file data/sample_training_data.csv \
  --string_file data/9606.protein.links.v12.0.txt \
  --alias_file data/9606.protein.aliases.v12.0.txt \
  --mm_genes_file data/mm_genes.txt \
  --output_dir results/
```

Training will take 30-60 minutes depending on:
- Data size
- Hardware (GPU recommended)
- Number of epochs (default: 100)

### Step 4: Use Your Trained Model

```bash
python test_model.py \
  --test_file your_vus_data.csv \
  --model_file results/trained_model.pkl \
  --network_file results/network_data.pkl \
  --output_dir test_results/
```

---

## 📊 Understanding the Output

### Key Files

1. **all_predictions.csv** - Complete ranked list
   - `ranking_score`: Higher = more pathogenic (for ranking)
   - `pathogenic_proba`: 0-1 probability (for interpretation)

2. **seed_gene_predictions.csv** - Variants in known MM genes
   - DDB1, CRBN, BCL2, FGFR3, KRAS, NRAS, TP53, etc.

3. **novel_gene_predictions.csv** - Candidate disease genes
   - Filtered for super-hub genes (ribosomal, housekeeping)
   - Sorted by ranking score

4. **high_risk_predictions.csv** - Probability > 0.7
   - Immediate candidates for follow-up

### Visualization

`test_predictions_distribution.png` shows:
- Score distribution across all variants
- Seed vs Novel gene comparison

---

## 💡 Tips

### Using Your Own MM Genes

Create a custom gene list:
```
# my_genes.txt
KRAS
NRAS
TP53
BRAF
ATM
...
```

Use it:
```bash
python predict_with_pretrained.py \
  --vus_file your_data.csv \
  --mm_genes my_genes.txt
```

### Large VUS Files

For large files (>100K variants):
- Use `--batch_size 64` for faster processing
- Results are automatically filtered for exonic/splicing
- Synonymous SNVs are excluded

### Probability Thresholds

Recommended interpretation:
- **>0.7**: High-risk (immediate follow-up)
- **0.4-0.7**: Medium-risk (consider for validation)
- **<0.4**: Low-risk

But **use ranking_score for prioritization**, not just probability!

---

## 🆘 Troubleshooting

### Error: "Model file not found"
```bash
# Download pre-trained model from GitHub Releases
# Or train your own model first
```

### Error: "Column X not found"
```bash
# Check your ANNOVAR annotation includes all 19 features
# See data/README.md for required columns
```

### Error: "No variants after filtering"
```bash
# Ensure your data includes:
# - Func.refGene = "exonic" or "splicing"
# - ExonicFunc.refGene != "synonymous SNV"
```

### Out of Memory
```bash
# Reduce batch size
python predict_with_pretrained.py \
  --vus_file your_data.csv \
  --batch_size 16
```

---

## 📚 Next Steps

- Read the full [README.md](../README.md) for detailed documentation
- Check [examples/](../examples/) for more usage examples
- See [data/README.md](../data/README.md) for data preparation
- Review [examples/example_outputs/](../examples/example_outputs/) for output examples

---

## 📧 Need Help?

- Open an [GitHub Issue](https://github.com/yourusername/mm-pathpredict/issues)
- Check existing issues for solutions
- Read [CONTRIBUTING.md](../CONTRIBUTING.md) for development guidelines

---

**Happy predicting!** 🧬✨

# Start Guide


## Prerequisites

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

### Step 1: Download Sample Training Data

Option 1: Prepare your own data
- ClinVar or other labeled data (Pathogenic/Benign)
- ANNOVAR-annotated
- At least 500 variants (balanced P/B)
- completed ANNOVAR annotation

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

<img width="777" height="683" alt="image" src="https://github.com/user-attachments/assets/1c6f703d-95a4-4c2c-9d75-90cf97b97065" />


Training will take 30-60 minutes depending on:
- Data size
- Hardware (GPU recommended)
- Number of epochs (default: 100)

### Step 4: Use Your Trained Model

<img width="507" height="152" alt="image" src="https://github.com/user-attachments/assets/ee6a23aa-2944-4e95-8586-23297fe7c641" />

---

## 📊 Understanding the Output

### Key Files

1. **all_predictions.csv** - Complete ranked list
   - `ranking_score`: Higher = more pathogenic (for ranking)
   - `pathogenic_proba`: 0-1 probability (for interpretation)

2. **seed_gene_predictions.csv** - Variants in known MM genes

3. **novel_gene_predictions.csv** - Candidate disease genes
   - Filtered for super-hub genes (ubiquitin, ribosomal, housekeeping)
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

Create a custom gene list and insert to there.
<img width="659" height="226" alt="image" src="https://github.com/user-attachments/assets/8b3b7661-0876-467d-a20a-5b7ec565610f" />

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

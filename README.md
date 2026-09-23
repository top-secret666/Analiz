<div align="center">

# Analiz

**RB1 pathogenic mutation classifier**

CNN on DNA windows · ClinVar labels · TensorFlow / Keras

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.15-FF6F00?style=flat-square&logo=tensorflow&logoColor=white)](https://www.tensorflow.org)
[![Keras](https://img.shields.io/badge/Keras-CNN-D00000?style=flat-square&logo=keras&logoColor=white)](https://keras.io)

Conference neural-network project · fork of [xDefox/Analiz](https://github.com/xDefox/Analiz)

</div>

---

Classifies **pathogenic vs normal** DNA fragments around the **RB1** tumor-suppressor gene using a 1D CNN. Training labels come from **ClinVar**; the reference sequence is loaded from FASTA.

Repo: [github.com/top-secret666/Analiz](https://github.com/top-secret666/Analiz)

## What it does

1. Loads the RB1 FASTA sequence and ClinVar mutation table
2. Builds a balanced dataset of 500-bp windows (mutation / normal)
3. Encodes bases as integers (`A=0, T=1, G=2, C=3`)
4. Trains a CNN with class weights, early stopping, and LR scheduling
5. Reports accuracy, precision, recall, and AUC — then plots training curves
6. Predicts mutation probability for new sequence windows

**Target metrics:** AUC > 0.85 · Precision / Recall > 0.8

## Stack

| Package | Role |
| --- | --- |
| TensorFlow / Keras | CNN model & training |
| NumPy · Pandas | Arrays & ClinVar CSV |
| Biopython | FASTA (`SeqIO`) |
| scikit-learn | Class weights |
| Matplotlib | Metric plots |

Pinned versions: see [`requirements.txt`](requirements.txt)

## Project layout

```text
.
├── rb1_classifier.py.py   # Train + evaluate + predict
├── RB1_mutations.csv      # ClinVar RB1 variants
├── requirements.txt
├──  data/raw/
│   ├── RB1.fna            # Gene sequence (FASTA)
│   └── clinvar_rb1.csv
└── models/
    ├── rb1_model.h5
    ├── rb1_model.keras
    └── rb1_mutation_predictor.keras
```

> Note: the data folder name starts with a space (` data/`). The script path matches that.

## Quick start

```bash
git clone https://github.com/top-secret666/Analiz.git
cd Analiz

python -m venv .venv
# Windows: .venv\Scripts\activate
source .venv/bin/activate

pip install -r requirements.txt
python rb1_classifier.py.py
```

**Needs:** Python 3.x · ~10–30 min on CPU for a full train run

Before running, update the FASTA path in `load_rb1_sequence()` if your checkout layout differs from the hardcoded path.

## Model

```text
Input (500,)
  → Embedding(4 → 8)
  → Conv1D(32, k=15) → MaxPool → Dropout(0.3)
  → Conv1D(64, k=7) → GlobalMaxPool
  → Dense(32, ReLU) → Dense(1, sigmoid)
```

Loss: binary cross-entropy · Optimizer: Adam (`lr=0.001`)

## Data sources

- **ClinVar** — [ncbi.nlm.nih.gov/clinvar](https://www.ncbi.nlm.nih.gov/clinvar) (RB1 pathogenic / likely pathogenic)
- **RB1.fna** — gene reference sequence in FASTA

To improve the model: download the full ClinVar RB1 set (~375 variants; the included table has fewer) and retrain with a larger sample count and cross-validation.

## Tips

- Ideal window size and sample count depend on your data — start with `n_samples=2000+`
- Prefer 5000+ examples and k-fold CV for stronger estimates
- Saved weights live under `models/` (`.h5` / `.keras`)

---

<div align="center">

Python · TensorFlow · Biopython  
[github.com/top-secret666/Analiz](https://github.com/top-secret666/Analiz)

</div>

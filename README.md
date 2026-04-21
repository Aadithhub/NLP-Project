# 🛡️ ADEGuard: Adverse Drug Event Detection and Severity Classification

> **Domain:** Healthcare NLP / Pharmacovigilance  
> **Model:** BioBERT + QLoRA | **Data:** VAERS 2020–2025 | **Task:** NER + Hierarchical Severity Classification

---

## 📌 Project Overview

ADEGuard is an end-to-end NLP pipeline that automatically detects **Adverse Drug Events (ADEs)** and **Drug mentions** from free-text vaccine adverse event reports (VAERS), and classifies each report into a severity level — **mild**, **moderate**, or **severe**.

The pipeline combines:
- **BioBERT-based NER** for ADE/Drug span extraction using BIO-tagging
- **Weak supervision** to scale annotations across 200K+ unlabeled reports
- **Agglomerative Hierarchical Clustering** for clinical pattern discovery
- **Two-stage Hierarchical Severity Classification** using BioBERT + QLoRA fine-tuning

---

## 🏗️ Pipeline Architecture

```
VAERS Reports (2020–2025)
        ↓
Data Preprocessing & Merging
        ↓
Gold Annotation (BIO-tag format)
        ↓
BioBERT NER Fine-tuning
        ↓
Weak Supervision (250K records labeled)
        ↓
Hierarchical Clustering (Agglomerative)
        ↓
Level-1 Classifier: mild vs non-mild     [F1: 0.84]
        ↓ (non-mild only)
Level-2 Classifier: moderate vs severe   [F1: 0.80]
```

---

## 📊 Results

### NER — Named Entity Recognition
| Entity | Notes |
|--------|-------|
| ADE    | Adverse Drug Event spans extracted via BIO-tagging |
| DRUG   | Drug/vaccine name spans extracted via BIO-tagging |

### Hierarchical Severity Classification

**Level-1: mild vs non-mild**
| Class | Precision | Recall | F1 |
|-------|-----------|--------|----|
| mild | 0.84 | 0.85 | 0.84 |
| non-mild | 0.85 | 0.84 | 0.84 |
| **Overall Accuracy** | | | **0.84** |

**Level-2: moderate vs severe**
| Class | Precision | Recall | F1 |
|-------|-----------|--------|----|
| moderate | 0.81 | 0.87 | 0.84 |
| severe | 0.79 | 0.70 | 0.74 |
| **Overall Accuracy** | | | **0.80** |

---

## 📁 Repository Structure

```
ADEGuard/
│
├── NLP_Project.ipynb               # Main notebook (full pipeline)
│
├── sample_data/
│   ├── sample_vaers_records.csv    # Sample VAERS records (100 rows)
│   ├── sample_gold_annotations.txt # Sample BIO-tag gold annotations
│   └── sample_weak_labels.parquet  # Sample weak labeled output
│
├── requirements.txt                # All dependencies
└── README.md                       # This file
```

> ⚠️ **Full dataset is not included** due to size constraints.  
> 📂 Access the complete processed data and model checkpoints here:  
> **[Google Drive Link](YOUR_DRIVE_LINK_HERE)** ← replace with your actual link

---

## 🗂️ Data Source

**VAERS (Vaccine Adverse Event Reporting System)**  
- Years: 2020–2025  
- Files per year: `VAERSDATA`, `VAERSSYMPTOMS`, `VAERSVAX`  
- Download from: [https://vaers.hhs.gov/data/datasets.html](https://vaers.hhs.gov/data/datasets.html)  
- Total records processed: **200K+**

---

## ⚙️ Setup & Installation

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_USERNAME/ADEGuard.git
cd ADEGuard
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Mount Google Drive (if using Colab)
```python
from google.colab import drive
drive.mount('/content/drive')
```

### 4. Run the notebook
Open `NLP_Project.ipynb` in Google Colab and run cells sequentially.

> 💡 **Recommended:** Google Colab with T4 GPU (free tier is sufficient)

---

## 🧠 Models Used

| Model | Purpose |
|-------|---------|
| `dmis-lab/biobert-base-cased-v1.1` | NER + Severity Classification base |
| `all-MiniLM-L6-v2` | Sentence embeddings for clustering |
| LoRA (r=16, α=32) | Parameter-efficient fine-tuning |
| Optimum Quanto (qint8) | 8-bit quantization (QLoRA-style) |

---

## 🔬 Key Techniques

- **BIO Tagging** — Token-level NER with B-ADE, I-ADE, B-DRUG, I-DRUG, O labels
- **Weak Supervision** — BioBERT NER predictions on 250K unlabeled records as pseudo-labels
- **Agglomerative Hierarchical Clustering** — Ward linkage clustering of clinical presentations
- **QLoRA Fine-tuning** — 8-bit quantized BioBERT base + trainable LoRA adapters
- **Hierarchical Classification** — Two-stage severity triage pipeline

---

## 📦 Requirements

See [`requirements.txt`](requirements.txt) for full list.

Core packages:
- `transformers`
- `datasets`
- `peft`
- `optimum`
- `quanto`
- `sentence-transformers`
- `scikit-learn`
- `pandas`
- `torch`
- `seqeval`

---

## 📌 Limitations

- Gold annotations are limited in size (few hundred sentences) — NER F1 on ADE class is modest
- Weak labels have sparse ADE coverage (~17%) due to high confidence threshold during prediction
- Severity labels are proxy-based (derived from VAERS flags) — not clinically validated ground truth

---

## 👤 Author

**Your Name**  
[LinkedIn](YOUR_LINKEDIN) | [GitHub](YOUR_GITHUB)

---

## 📄 License

This project is for academic research purposes only.  
VAERS data is publicly available from the CDC/FDA.

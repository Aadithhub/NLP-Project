# 🛡️ ADEGuard: Adverse Drug Event Detection and Severity Classification

> **Domain:** Healthcare NLP / Pharmacovigilance  
> **Model:** BioBERT + QLoRA | **Data:** VAERS 2020–2025 | **Task:** NER + Hierarchical Severity Classification

---

## 📌 Project Overview

ADEGuard is an end-to-end NLP pipeline that automatically detects **Adverse Drug Events (ADEs)** and **Drug mentions** from free-text vaccine adverse event reports (VAERS), and classifies each report into a severity level — **mild**, **moderate**, or **severe**.

The pipeline combines:
- **BioBERT-based NER** for ADE/Drug span extraction using BIO-tagging
- **Weak supervision** to scale annotations across 200K+ unlabeled reports
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
Weak Supervision (250K records labeled)
        ↓
Hybrid Training Dataset (Gold ×3 + Weak labels)
        ↓
BioBERT NER Fine-tuning
        ↓
Level-1 Classifier: mild vs non-mild     [F1: 0.84]
        ↓ (non-mild only)
Level-2 Classifier: moderate vs severe   [F1: 0.80]
```

---

## 📊 Results

### NER — Named Entity Recognition
| Entity | Precision | Recall | F1-Score |
|--------|-----------|--------|----------|
| ADE | 0.46 | 0.42 | 0.44 |
| DRUG | 0.69 | 0.91 | 0.79 |

### Hierarchical Severity Classification

**Level-1: mild vs non-mild — Accuracy: 0.84**
| Class | Precision | Recall | F1-Score |
|-------|-----------|--------|----------|
| Mild | 0.84 | 0.85 | 0.84 |
| Non-mild | 0.85 | 0.84 | 0.84 |

**Level-2: moderate vs severe — Accuracy: 0.80**
| Class | Precision | Recall | F1-Score |
|-------|-----------|--------|----------|
| Moderate | 0.81 | 0.87 | 0.84 |
| Severe | 0.79 | 0.70 | 0.74 |

---

## 📁 Repository Structure

```
ADEGuard/
│
├── Code/
│   └── NLP_Project_code.ipynb          # Main notebook (full pipeline)
│
├── Data/
│   └── NLP_Project_Sample_data.csv     # Sample from merged VAERS dataset
│
├── Results/
│   └── NLP_results.pdf                 # Problem statement, introduction,
│                                       # objective, methodology & results
│
├── requirements.txt                    # All dependencies
└── README.md                           # This file
```

---

## 🗂️ Data Source

**VAERS (Vaccine Adverse Event Reporting System)**
- Years: 2020–2025
- Files per year: `VAERSDATA`, `VAERSSYMPTOMS`, `VAERSVAX`
- Download from: [https://vaers.hhs.gov/data/datasets.html](https://vaers.hhs.gov/data/datasets.html)
- Total records processed: **200K+**

> ⚠️ Full dataset is not included due to size constraints.  
> A sample of the merged dataset is available in the `Data/` folder.

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
Open `Code/NLP_Project_code.ipynb` in Google Colab and run cells sequentially.

> 💡 **Recommended:** Google Colab with T4 GPU (free tier is sufficient)

---

## 🧠 Models Used

| Model | Purpose |
|-------|---------|
| `dmis-lab/biobert-base-cased-v1.1` | NER + Severity Classification base |
| `all-MiniLM-L6-v2` | Sentence embeddings for weak supervision |
| LoRA (r=16, α=32) | Parameter-efficient fine-tuning |
| Optimum Quanto (qint8) | 8-bit quantization (QLoRA-style) |

---

## 🔬 Key Techniques

- **BIO Tagging** — Token-level NER with B-ADE, I-ADE, B-DRUG, I-DRUG, O labels
- **Weak Supervision** — BioBERT NER predictions on 250K unlabeled records as pseudo-labels
- **Hybrid Training** — Gold samples (weighted ×3) combined with weak labels for robust NER fine-tuning
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

- Gold annotations are limited in size (525 samples) — NER F1 on ADE class is modest due to class imbalance
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

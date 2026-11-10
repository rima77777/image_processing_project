# 🖼️ RAG-Based Image Captioning Pipeline (MS COCO)

An implementation of a Retrieval-Augmented Generation (RAG) system for automated image captioning using **FAISS** vector indexing and **Google Flan-T5**.

## 📌 Features
* **Vector Store Indexing**: Scalable indexing of MS COCO image embeddings using FAISS (`IndexFlatIP`).
* **Top-k Retrieval**: Fetches top-$k$ contextual reference captions based on visual similarity.
* **Conditioned Generation**: Uses `google/flan-t5-small` (`AutoModelForSeq2SeqLM`) to produce grounded image captions.
* **Comparative Evaluation & Audit**: Automated metric calculation (**BLEU** and **ROUGE-L**) alongside a data leakage audit.

---

## 📁 Repository Structure
```text
.
├── rag_captioning_pipeline.py            # Main RAG indexing, retrieval, and generation module
├── evaluate_captions.py                  # Evaluation script computing BLEU & ROUGE-L
├── final_rag_vs_baseline_evaluation.json # JSON record of baseline vs. RAG outputs
├── leakage_audit.json                    # Data contamination / train-test leakage check log
├── project_summary_and_justification.txt # Technical documentation & design rationale
└── README.md                             # Project overview and usage instructions
```

---

## 🚀 Quickstart Guide

### 1. Install Dependencies
```bash
pip install torch transformers faiss-cpu evaluate nltk rouge-score
```

### 2. Run Main Pipeline
```bash
python rag_captioning_pipeline.py
```

### 3. Run Metric Evaluation
```bash
python evaluate_captions.py
```

# Group 6 — RAG-Based Image Captioning (MS COCO)

## 1. Project Overview

This project explores image captioning using Retrieval-Augmented Generation (RAG), combining a caption knowledge base, embeddings, FAISS similarity search, BM25 retrieval, and FLAN-T5 text generation.

Main components:
- MS COCO caption data and image metadata.
- YOLOv8m object-detection metadata.
- Sentence-BERT and OpenCLIP embeddings.
- FAISS similarity indexes.
- BM25 caption retrieval.
- Google FLAN-T5-small for text generation.
- BLEU and ROUGE evaluation utilities.

## 2. Repository Structure

```text
.
├── data/
├── src/
├── splits/
├── embeddings/
├── index/
├── exploration/
├── report/
├── docs/
├── rag_captioning_pipeline.py
├── evaluate_captions.py
├── requirements.txt
└── README.md
```

## 3. Dataset and Data Quality

| Partition | Images | Captions |
|---|---:|---:|
| Knowledge Base | 10,000 | 49,777 after exclusion |
| Validation | 100 | 500 |
| Test | 1,000 | 5,000 |

The original knowledge base contained 50,000 captions. A leakage audit identified 223 captions for exclusion, resulting in 49,777 captions.

The cleaned dataset is stored in `data/captions_processed_v1.1.json`.

See `splits/README.md` for split details.

## 4. Object Detection and Exploration

Object metadata is stored in `data/metadata.json` and was generated using YOLOv8m with a confidence threshold of 0.25.

- Metadata image records: 11,100
- Detected objects: 78,381
- Detected classes: 80
- Average objects per image: 7.06
- Mean detection confidence: 0.6458
- Median detection confidence: 0.682

Exploration statistics and visualizations are saved in `exploration/`.

These detections are automatically generated and have not been individually verified by human annotators.

## 5. Embeddings and Retrieval

The project uses OpenCLIP ViT-B-32 embeddings (512 dimensions), Sentence-BERT (`all-MiniLM-L6-v2`, 384 dimensions) in the retrieval workflow, FAISS similarity indexes, and BM25 lexical retrieval.

Embedding and index files are stored in `embeddings/` and `index/`. Check `index/index_config.json` and verify that each required index exists before running experiments.

## 6. Generation and Evaluation

The generation component uses `google/flan-t5-small` to generate captions from a visual description and retrieved textual evidence.

- `rag_captioning_pipeline.py`: RAG pipeline implementation.
- `evaluate_captions.py`: evaluation utilities.
- `report/`: audit and experimental outputs.

A five-example pilot is stored in `report/metadata_based_pilot_5.json`.

Important limitation: this pilot uses metadata-based visual descriptions rather than direct original-image inputs. It is not a full end-to-end image-captioning evaluation.

## 7. Installation

Install dependencies using:

```bash
pip install -r requirements.txt
```

Some packages, especially PyTorch and FAISS, may require environment-specific installation choices.

## 8. Running the Project

The project was developed in Google Colab.

1. Open the repository in Google Colab.
2. Install dependencies from `requirements.txt`.
3. Review `src/config.py` and configure project paths.
4. Read `splits/README.md`.
5. Run the data audit and split scripts when reproducing dataset preparation.
6. Verify embedding files and FAISS indexes before retrieval.
7. Run the pipeline and evaluation scripts after checking their input requirements.
8. Save experiment outputs in `report/` and exploration outputs in `exploration/`.

Run scripts from the repository root and check their configuration before execution.

## 9. Reproducibility and Limitations

- The cleaned knowledge base contains 49,777 captions after excluding 223 leakage-related entries.
- The KB, validation, and test partitions contain 10,000, 100, and 1,000 images.
- YOLO metadata is automatically generated, not manually annotated.
- Metadata-based pilot results are not equivalent to evaluation using original images.
- Verify index availability and retrieval outputs before reporting final performance.
- Small pilot experiments are not conclusive evidence of overall model superiority.

## 10. Project Documentation

- `splits/README.md`: dataset split documentation.
- `docs/section_1_data_audit_and_splits.md`: data audit and split methodology.
- `docs/section_4_3_embeddings_and_kb.md`: embeddings and knowledge-base documentation.
- `exploration/`: generated statistics and visualizations.
- `report/`: audit and experimental outputs.

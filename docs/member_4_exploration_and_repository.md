# Member 4 — Exploration, Analysis, and Repository Documentation

## 1. Caption Dataset Exploration

The knowledge base initially contained 10,000 images and 50,000 captions. A leakage audit identified 223 captions that were excluded to reduce possible overlap-related contamination between dataset partitions.

After exclusion, the knowledge base contained 49,777 captions associated with the same 10,000 images. The cleaned dataset was saved as `data/captions_processed_v1.1.json`.

### 1.1 Caption Statistics Before and After Exclusion

| Statistic | Before exclusion | After exclusion |
|---|---:|---:|
| Number of images | 10,000 | 10,000 |
| Number of captions | 50,000 | 49,777 |
| Average words per caption | 10.4643 | 10.4694 |
| Unique captions | 49,372 | 49,268 |

The exclusion removed 223 captions. The average caption length changed only slightly, from approximately 10.46 to 10.47 words. This indicates that the exclusion primarily affected the number of captions rather than substantially changing their average length.

This comparison measures the effect of leakage exclusion. It should not be interpreted as a comparison of raw text against linguistically normalized or cleaned text.

The corresponding results are saved in:
- `exploration/caption_statistics.csv`
- `exploration/caption_exclusion_comparison.csv`

## 2. Object Detection Metadata Analysis

Object-level metadata was generated using YOLOv8m with a confidence threshold of 0.25. The metadata file contains records for the knowledge base, validation, and test images.

### 2.1 Detection Statistics

| Metric | Result |
|---|---:|
| Metadata image records | 11,100 |
| Total detected objects | 78,381 |
| Number of detected classes | 80 |
| Average objects per image | 7.06 |
| Mean detection confidence | 0.6458 |
| Median detection confidence | 0.682 |
| Minimum detection confidence | 0.25 |
| Maximum detection confidence | 0.992 |

The most frequently detected class was `person`, with 24,670 detections.

The average of approximately seven detections per image provides an indication of the amount of object-level information available in the metadata. The mean confidence score was approximately 64.58%.

These detections were automatically generated and were not individually verified by human annotators. Therefore, detection confidence should not be interpreted as a guarantee of object-label correctness.

The statistics are stored in:
- `exploration/object_statistics.json`
- `exploration/object_class_statistics.csv`

## 3. Data Exploration Visualizations

Several visualizations were generated to support the statistical analysis:

- `exploration/visualizations/caption_length_distribution.png` — caption-length distribution.
- `exploration/visualizations/top_15_object_classes.png` — the 15 most frequent detected object classes.
- `exploration/visualizations/objects_per_image.png` — distribution of detected objects per image.
- `exploration/visualizations/caption_before_after.png` — caption statistics before and after exclusion.

These figures provide a visual overview of caption characteristics, object categories, and the effect of leakage exclusion.

## 4. Metadata-Based Pilot Analysis

A five-image pilot was saved in `report/metadata_based_pilot_5.json`. It compares baseline captions with RAG-generated captions using metadata-based visual descriptions and retrieved textual evidence.

This experiment has an important limitation: the original images were not directly supplied to the generation model. Instead, the experiment used textual descriptions derived from object metadata.

Consequently, the pilot evaluates a metadata-based caption-generation workflow rather than complete end-to-end image captioning. Its five examples are exploratory and are insufficient to establish general performance superiority of either approach.

## 5. Repository Organization and Documentation

The repository was organized into directories according to their roles:

- `data/` contains caption data and object metadata.
- `src/` contains data audit, configuration, and split-generation scripts.
- `splits/` contains dataset manifests, identifiers, and exclusion information.
- `embeddings/` contains embedding arrays and knowledge-base mappings.
- `index/` contains FAISS indexes and index configuration.
- `exploration/` contains statistical tables and visualizations.
- `report/` contains audit records and experimental outputs.
- `docs/` contains project documentation and report sections.

The root `README.md` was rewritten to explain the project objectives, repository structure, dataset statistics, installation procedure, execution workflow, and experimental limitations.

A `requirements.txt` file was also created to record the package versions used in the development environment. It includes the principal libraries used for numerical processing, embeddings, retrieval, object detection, generation, and evaluation.

## 6. Reproducibility and Limitations

The exploration outputs were saved as CSV, JSON, and PNG files so that the results can be inspected without rerunning every analysis.

The following limitations should be considered:

1. YOLO detections are automatically generated and have not been manually validated.
2. The caption comparison describes leakage exclusion, not necessarily linguistic text preprocessing.
3. The five-image pilot is metadata-based and does not constitute direct image-based evaluation.
4. Small pilot experiments should not be treated as conclusive evidence of overall model performance.
5. Package versions reflect the development environment; installation may require adjustments for other hardware or operating systems.

Overall, the exploration provides a quantitative description of the caption knowledge base and object metadata, while the repository documentation improves project organization and reproducibility.

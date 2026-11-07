# Data Audit & Frozen Splits (v1.0)

Phase 1, Part 1 of the project **A comparative study of RAG techniques for image captioning** (Group 6 – Sparse / BM25 RAG).

This part provides the **shared, audited and frozen splits** used by every later step: the RAG knowledge base, hyperparameter tuning and final evaluation.

---

## 1. Summary

| Split | Source | Images | Captions | Purpose |
|---|---|---|---|---|
| `kb` | COCO train2017 | 10,000 | 50,000 | RAG knowledge base |
| `val` | COCO train2017 (disjoint from `kb`) | 100 | 500 | Hyperparameter tuning |
| `test` | COCO val2017 (stratified) | 1,000 | 5,000 | Final evaluation only |

- Random seed: **42**
- Captions: first **5** per image, ordered by annotation ID
- Total train2017 images used: 10,100 (`kb` and `val` are drawn from one shuffled pool, so they cannot overlap)
- Test stratification by scene complexity (number of annotated objects):

| Stratum | Objects / image | Pool | Selected |
|---|---|---|---|
| Simple | ≤ 3 | 2,062 | 300 |
| Medium | 4 – 7 | 1,192 | 400 |
| Complex | ≥ 8 | 1,698 | 300 |

---

## 2. Pipeline

```
COCO annotations + images
        │
        ▼
 [1] Data audit  ──────────►  reports/audit_report.json
        │
        ▼
 [2] Candidate pools (≥ 5 captions per image)
        │
        ▼
 [3] Seeded sampling  +  image check (decode, EXIF → RGB, MD5)
        │              unreadable / duplicate content → rejected and replaced
        ▼
 [4] Split creation  ─────►  splits/*.json
        │
        ▼
 [5] Leakage audit  ──────►  reports/leakage_audit.json / .md
        │
        ▼
 [6] Freeze  ─────────────►  release v1.0
```

### 2.1 Data audit
Checked on the caption files of train2017 and val2017:
number of images and captions, duplicated image IDs, images missing on disk, orphan captions, empty captions, captions per image.

| Check | train2017 | val2017 |
|---|---|---|
| Images | 118,287 | 5,000 |
| Captions | 591,753 | 25,014 |
| Duplicated image IDs | 0 | 0 |
| Images missing on disk | 0 | 0 |
| Orphan / empty captions | 0 / 0 | 0 / 0 |
| Images with < 5 captions | 0 | 0 |

A few images have 6 or 7 captions (train2017: 312 with 6 and 3 with 7; val2017: 12 with 6 and 1 with 7). Only the first 5 are kept so every image has the same structure.

### 2.2 Image integrity
Every selected image is opened, fully decoded, converted to RGB after EXIF orientation correction, and hashed (MD5). Unreadable files or duplicated contents are rejected and replaced by the next candidate.
**Result: 0 unreadable, 0 duplicated, no replacement needed.**

### 2.3 Leakage audit

| Check | Result |
|---|---|
| ID overlap `kb` / `val`, `kb` / `test`, `val` / `test` | 0 / 0 / 0 |
| Content overlap `kb` / `test` | 0 |
| Content overlap `val` / `kb` | 0 |

Test reference captions exist only in `test_manifest.json` and never enter the knowledge base. Since no test image is indexed, it cannot be retrieved for itself.
Details: [`reports/leakage_audit.md`](../reports/leakage_audit.md).

---

## 3. Files

```
splits/
├── kb_ids.json            sorted image IDs of the knowledge base
├── val_ids.json           sorted image IDs of the validation set
├── test_ids.json          sorted image IDs of the test set
├── kb_manifest.json       image_id, file_name, 5 captions
├── val_manifest.json      image_id, file_name, 5 captions
├── test_manifest.json     image_id, file_name, 5 captions, stratum
└── split_meta.json        seed, quotas, caption policy, rejected images
reports/
├── audit_report.json      data audit + selected-images check
├── leakage_audit.json     raw leakage results
└── leakage_audit.md       leakage audit summary
notebooks/
└── 01_data_audit_and_splits.ipynb
```

Manifest entry example:

```json
{
  "image_id": 139,
  "file_name": "000000000139.jpg",
  "captions": ["...", "...", "...", "...", "..."],
  "stratum": "medium"
}
```
(`stratum` is present in `test_manifest.json` only.)

---

## 4. Usage

```python
import json

kb   = json.load(open("splits/kb_manifest.json"))
val  = json.load(open("splits/val_manifest.json"))
test = json.load(open("splits/test_manifest.json"))

print(len(kb), len(val), len(test))   # 10000 100 1000
```

Image files are **not** stored in this repository. Load them from COCO 2017
(`train2017/` for `kb` and `val`, `val2017/` for `test`) using `file_name`.

---

## 5. Reproduction

1. Open `notebooks/01_data_audit_and_splits.ipynb` on Kaggle.
2. Add the COCO 2017 dataset as input (used here: `sabahesaraki/2017-2017`).
3. Run all cells (about 5 minutes). The fixed seed regenerates exactly the same splits.
4. The notebook ends with assertions on split sizes and zero overlap.

---

## 6. Rules for all groups

- Use `kb` as the only retrieval corpus.
- Use `val` for hyperparameter tuning and `test` for final evaluation only.
- Never use test reference captions in retrieval, indexing, prompting or tuning.
- Never modify the frozen files.

---

## 7. Limitations

- Duplicate detection uses MD5 and finds **byte-identical** files only. Near-duplicates (resized, re-encoded or visually very similar images) are not detected.
- Content checks cover the selected images (about 11,100), not all 118,287 train2017 images.
- Complexity strata are based on the number of annotated objects only; they do not capture interactions or context.

---

## 8. Version

| Version | Description |
|---|---|
| v1.0 | Frozen splits: 10,000 / 100 / 1,000, audit and leakage reports |

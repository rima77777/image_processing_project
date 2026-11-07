# Leakage Audit Summary – Splits v1.0

**Scope:** frozen splits `kb` (knowledge base), `val` (validation / tuning) and `test`.
**Raw results:** `reports/leakage_audit.json` and `reports/audit_report.json`.
**Notebook:** `notebooks/01_data_audit_and_splits.ipynb`.

## 1. Split definition

| Split | Source | Images | Captions |
|---|---|---|---|
| Knowledge base (`kb`) | COCO train2017 | 10,000 | 50,000 |
| Validation (`val`) | COCO train2017, disjoint from `kb` | 100 | 500 |
| Test (`test`) | COCO val2017, stratified (300 simple / 400 medium / 300 complex) | 1,000 | 5,000 |

Seed: 42. Captions: first 5 per image, ordered by annotation ID.
`kb` and `val` were drawn from one shuffled pool of train2017 (10,100 distinct images), so they are disjoint by construction.

## 2. Checks performed

1. **ID overlap:** set intersections between every pair of splits.
2. **Content overlap:** MD5 hash of every selected image; hashes of train2017 selections (`kb` + `val`) compared with hashes of `test`, and hashes of `val` compared with hashes of `kb`.
3. **Image integrity:** every selected image opened and decoded (RGB, EXIF orientation corrected); unreadable or duplicated-content candidates would have been rejected and replaced.
4. **Caption separation:** reference captions of test images exist only in `splits/test_manifest.json`; `kb_manifest.json` contains train2017 images only.

## 3. Results

| Check | Result | Expected |
|---|---|---|
| `kb` size | 10,000 | 10,000 |
| `val` size | 100 | 100 |
| `test` size | 1,000 | 1,000 |
| ID overlap `kb` / `val` | 0 | 0 |
| ID overlap `kb` / `test` | 0 | 0 |
| ID overlap `val` / `test` | 0 | 0 |
| Content overlap `kb` / `test` | 0 | 0 |
| Content overlap `val` / `kb` | 0 | 0 |
| Rejected: unreadable | 0 | – |
| Rejected: duplicated content | 0 | – |

All assertions in the notebook passed. No sample was removed or replaced.

## 4. Conclusion

No test image or test caption appears in the knowledge base, and the validation set shares no image with the knowledge base. Consequently, a test image cannot be retrieved for itself, since it is not indexed in the knowledge base.

## 5. Limitations

- MD5 detects byte-identical files only. Near-duplicates (resized, re-encoded or visually very similar images) are not detected.
- Content checks cover the selected images (about 11,100), not the full 118,287 train2017 images.

## 6. Rules for all groups

- Do not modify any file in `splits/`; any change must be released as a new version.
- Never use test reference captions in retrieval, indexing, prompting or tuning.
- Use `val` for hyperparameter tuning and `test` for final evaluation only.

# Column Reference

| Column name | Type | Description |
|-------------|------|-------------|
| `original_index` | *int* | Internal row index used while constructing the dataset. It is no longer required for modelling or evaluation. You may ignore it or drop it in downstream processing. |
| `hate_speech` | *string* | Hate-speech (HS) message text. |
| `counternarrative` | *string* | Counter-narrative (CN) proposed to counter the HS. |
| `is_appropriate` | *int* (0/1) | 1 = CN judged **appropriate** for the HS; 0 = **not** appropriate. |
| `is_valid` | *int* (0/1) | Indicates whether the validators agreed with the value in `is_appropriate` (see §3.4 *Validation process* in the paper). 1 = agreement; 0 = disagreement. |
| `hsType` | *string* | High-level hate-speech category from the **original** CONAN dataset. |
| `hsSubType` | *string* | Fine-grained subtype of the HS. |
| `cnType` | *string* | Counter-narrative category label from CONAN. |
| `age` | *int* | Age of the **original** CONAN HS annotator. |
| `gender` | *string* | Gender of the **original** CONAN HS annotator. |
| `educationLevel` | *string* | Education level of the **original** CONAN HS annotator. |
| `cn_id` | *int* | CN identifier in the **original** CONAN dataset. |
| `annotation of annotator 1 (0: non appropiate; 1: not sure; 2: appropiate counteranrrative)` | *int* (0/1/2) | Annotation by *Annotator 1*: 0 = not appropriate, 1 = uncertain, 2 = appropriate. |
| `annotation of annotator 2 (0: non appropiate; 1: not sure; 2: appropiate counteranrrative)` | *int* (0/1/2) | Annotation by *Annotator 2* (may be empty if only one annotator). |
| `annotation of annotator 3 (0: non appropiate; 1: not sure; 2: appropiate counteranrrative)` | *int* (0/1/2) | Annotation by *Annotator 3* (may be empty if < 3 annotators). |

### Notes

* **Missing values** in `ann2` / `ann3` are encoded as empty cells.
* Column names are identical across all partitions and splits.
* Data are UTF-8 encoded; CSVs use “,” as the delimiter and `\n` line endings.

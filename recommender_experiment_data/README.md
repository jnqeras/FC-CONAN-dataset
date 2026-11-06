# FC-CONAN — Retrieval Experiment Input Data

This directory contains the **released input CSVs** associated with the counter-narrative retrieval evaluation in Section 4.3 of [FC-CONAN: An Exhaustively Paired Dataset for Robust Evaluation of Retrieval Systems](https://arxiv.org/abs/2601.01350). See the [main README](../README.md) for an overview of the research and the [annotation partitions](../fc_conan_partitions/README.md) for relevance labels.

These files provide **training-data inputs, a fixed candidate catalogue, and HS queries**. They do not contain the trained models, their ranking outputs, or a complete executable reproduction of the twelve systems reported in the paper.

## Files and purposes

| File | Rows | Role |
| --- | ---: | --- |
| [`conan_not_in_bronze_train_df.csv`](conan_not_in_bronze_train_df.csv) | 3,119 | English CONAN-derived HS–CN pair data prepared as the experiment's training input. |
| [`fc_conan_bronze_cn.csv`](fc_conan_bronze_cn.csv) | 100 | Fixed candidate catalogue of 100 CN texts ranked by each evaluated approach. |
| [`fc_conan_bronze_hs.csv`](fc_conan_bronze_hs.csv) | 45 | HS query texts used to request top-10 CN recommendations. |

The reported experiment ranks the **same 100-candidate catalogue** for each HS query, then checks each recommended `(HS, CN)` pair against the appropriate labels in **Diamond, Gold, Silver, and Bronze**. A positive evaluation label depends on the pair, not on the CN alone. The quality partitions are not independent train/test splits.

## Exact CSV columns

### `conan_not_in_bronze_train_df.csv`

| Exact header | Meaning |
| --- | --- |
| `cn_id` | Original CONAN CN identifier; **string**, not integer. |
| `hateSpeech` | Hate-speech message text. |
| `counterSpeech` | Counter-narrative text. |
| `hsType` | HS category from CONAN metadata. |
| `hsSubType` | More specific HS category. |
| `cnType` | CN category. |
| `age` | Age metadata carried over from CONAN. |
| `gender` | Gender metadata carried over from CONAN. |
| `educationLevel` | Education metadata carried over from CONAN. |

### `fc_conan_bronze_cn.csv`

Single column: **`counterSpeech`** (CN text). Its 100 rows contain 100 distinct CN texts.

### `fc_conan_bronze_hs.csv`

Single column: **`hateSpeech`** (HS query text). Its 45 rows contain 45 distinct HS texts.

**Column naming differs between directories:** the partition CSVs use `hate speech` and `counternarrative`, while these experiment files use `hateSpeech` and `counterSpeech`. These are not interchangeable literal headers in Python code.

## Separation of training data and evaluation inputs

The manuscript (§4.3) describes building the training set by excluding any HS **or** CN appearing in Bronze. The current released CSVs warrant a more precise file-level description:

| Direct comparison of released files | Shared items |
| --- | ---: |
| Training HS vs. Bronze HS | **0** |
| Training CN vs. all Bronze CN | **30** |
| Training `(HS, CN)` pairs vs. Bronze `(HS, CN)` pairs | **0** |
| Training CN vs. the fixed 100-CN candidate catalogue | **0** |

Further inspection shows that the current Bronze file contains **130 distinct CN texts**. Exactly **100 CNs have at least one pair labeled appropriate** in Bronze, and those 100 are exactly the CNs in the published candidate catalogue. The other **30 CNs have no appropriate pairs in Bronze** and are the same 30 CNs shared with the released training set. This is an observed property of the public files, **not proof of the intended candidate-selection rule or of the original preprocessing procedure**.

Consequently, the released inputs are disjoint in their HS queries, their *evaluated candidate CNs*, and their exact HS–CN pairs, **but they are not CN-disjoint from the entire Bronze partition**. The difference between this file-level finding and the broader exclusion claim in the paper remains under review. No claim is made here that the observed overlap invalidates the published results; nor should the files be described as fully HS-and-CN disjoint.

This distinction concerns **CNs (counter-narratives)**: Bronze still contains 45 distinct HS messages. A separate discrepancy between the paper's **129 selected CNs** and the released Bronze file's **130 distinct CNs** also remains under review; see the [partition README](../fc_conan_partitions/README.md#methodological-scope-and-release-note).

## Inspect the inputs

From the repository root, using Python 3 (no third-party dependencies):

```python
import csv
from pathlib import Path

base = Path("recommender_experiment_data")

def read_csv(filename):
    with (base / filename).open(encoding="utf-8", newline="") as f:
        return list(csv.DictReader(f))

train = read_csv("conan_not_in_bronze_train_df.csv")
candidates = read_csv("fc_conan_bronze_cn.csv")
queries = read_csv("fc_conan_bronze_hs.csv")

train_cn = {row["counterSpeech"] for row in train}
candidate_cn = {row["counterSpeech"] for row in candidates}

print("Training rows:", len(train))                  # 3119
print("Candidate CNs:", len(candidate_cn))            # 100
print("HS queries:", len(queries))                   # 45
print("Training/candidate CN overlap:", len(train_cn & candidate_cn))  # 0
```

For a retrieval ranking, each query is a `hateSpeech` value and each candidate is a `counterSpeech` value. The paper evaluates the top ten returned candidates against each partition's pair-level appropriateness annotations; **the CSVs alone do not define how to train or invoke each model**.

## Citation and responsible use

Cite the [FC-CONAN workshop publication](https://jurisinformaticscenter.github.io/NeLaMKRR2025/proceedings.pdf#page=63), and the original CONAN source as appropriate. The dataset includes harmful language. Consult the [main README](../README.md#license-usage-and-attribution) for the current license/attribution statement; no independent license file is present in this repository.

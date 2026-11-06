# FC-CONAN — Annotation Partitions

This directory contains the four nested annotation-quality partitions released with **FC-CONAN: An Exhaustively Paired Dataset for Robust Evaluation of Retrieval Systems**. The same HS–CN pair can occur in multiple partitions: these files are **quality subsets, not independent train/test splits**.

See the [main project README](../README.md) and the [published paper](https://arxiv.org/abs/2601.01350) (Sections 3 and 4.1–4.2) for the scientific motivation and methodology.

## Available files

| Partition | CSV file | Rows | Appropriate (`1`) | Not appropriate (`0`) | Validator-confirmed (`1`) |
| --- | --- | ---: | ---: | ---: | ---: |
| Diamond | [`diamond_df/diamond_partition_df.csv`](diamond_df/diamond_partition_df.csv) | 551 | 35 | 516 | 551 |
| Gold | [`gold_df/gold_partition_df.csv`](gold_df/gold_partition_df.csv) | 663 | 54 | 609 | 663 |
| Silver | [`silver_df/silver_partition_df.csv`](silver_df/silver_partition_df.csv) | 3,580 | 431 | 3,149 | 3,580 |
| Bronze | [`bronze_df/bronze_partition_df.csv`](bronze_df/bronze_partition_df.csv) | 3,997 | 702 | 3,295 | 3,580 |

The *appropriateness* and *validator-confirmation* columns measure different things; the 417 Bronze rows with confirmation value `0` were **reviewed but not confirmed**. They are not unreviewed or necessarily labeled *not appropriate*. Counts correspond to Tables 1 and 2 in the paper and were checked against the released CSVs.

### Partition criteria

- **Diamond:** Two or more annotators agreed unanimously on the appropriateness label, and an independent validator confirmed it.
- **Gold:** Includes Diamond plus other pairs annotated by at least two annotators whose final/adjudicated label was confirmed by a validator.
- **Silver:** Includes Gold plus pairs labeled by a single annotator and **subsequently confirmed by a validator**. Every Silver row is validator-confirmed.
- **Bronze:** Includes Silver plus 417 pairs whose annotation was **not confirmed by the validator**. Every Bronze row was part of the validation process, but not every original annotation was confirmed.

The inclusion relationship is **Diamond ⊆ Gold ⊆ Silver ⊆ Bronze**. The CSV rows were verified to satisfy this nesting by their HS–CN text pairs.

## CSV schema: exact column names

**Important:** The headers below reproduce the CSV column names *exactly*, including spaces and original spelling. The names `hate_speech`, `is_appropriate`, `is_valid`, `ann2`, and `ann3` used as shorthand in older documentation **are not literal column names**.

### Pair and validation fields

| Exact CSV header | Values | Meaning |
| --- | --- | --- |
| `original_index` | Integer-like identifier | Original construction/index reference. Do not treat it as a stable cross-dataset identifier. |
| `hate speech` | Text | Hate-speech message (HS). Note the space in the header. |
| `counternarrative` | Text | Candidate counter-narrative (CN). |
| `Appropriate counternarrative (1) or non appropriate (0)` | `0` / `1` | Final pair-level appropriateness label: `1` means appropriate for this HS; `0` means not appropriate for this HS. |
| `Valid (1) / Non Valid (0) annotation` | `0` / `1` | Validator assessment of the annotation: `1` = confirmed; `0` = not confirmed. **Not** a second appropriateness label. |

### CONAN-derived metadata

| Exact CSV header | Values | Meaning |
| --- | --- | --- |
| `hsType` | Category text | HS category from the original CONAN metadata. |
| `hsSubType` | Category text | More specific HS category. |
| `cnType` | Category text | CN category. |
| `age` | Numeric text, e.g. `25.0` | Age field carried over from CONAN metadata. |
| `gender` | Text | Gender field carried over from CONAN metadata. |
| `educationLevel` | Text | Education field carried over from CONAN metadata. |
| `cn_id` | **String** (e.g. `ENT1ST0008HS0045CN000488`) | Original CONAN identifier for the CN. **Not an integer** and **not a unique row key**: a CN appears with several HS messages. |

### Individual annotator judgments

The last three columns have these exact headers (including the original typos):

```text
annotation of annotator 1 (0: non appropiate; 1: not sure; 2: appropiate counteranrrative)
annotation of annotator 2 (0: non appropiate; 1: not sure; 2: appropiate counteranrrative)
annotation of annotator 3 (0: non appropiate; 1: not sure; 2: appropiate counteranrrative)
```

Non-empty annotation values are `0` (not appropriate), `1` (unsure), and `2` (appropriate). **An empty cell means that no judgment was recorded in that particular annotator column.** It must not be interpreted as a negative label or filled with zero. These columns contain the individual judgments, not the adjudicated final label.

## Minimal read example

Run from the **repository root**, using only the Python 3 standard library:

```python
import csv
from pathlib import Path

path = Path("fc_conan_partitions/bronze_df/bronze_partition_df.csv")
with path.open(encoding="utf-8", newline="") as file:
    rows = list(csv.DictReader(file))

label = "Appropriate counternarrative (1) or non appropriate (0)"
confirmed = "Valid (1) / Non Valid (0) annotation"

print("Rows:", len(rows))                    # 3997
print("Appropriate:", sum(r[label] == "1" for r in rows))  # 702
print("Validator-confirmed:", sum(r[confirmed] == "1" for r in rows))  # 3580
```

When filtering, distinguish **validator-confirmed** (`confirmed == "1"`) from **appropriate** (`label == "1"`). For instance, some Bronze pairs are labeled appropriate even though their annotations were not confirmed by a validator.

## Methodological scope and release note

The paper describes an annotation design covering all **45 × 129 = 5,805** candidate HS–CN combinations, followed by annotation, adjudication, and validation exclusions. Thus, **3,997 Bronze rows does not mean 5,805 rows should appear in the released CSV**.

**Provenance clarification under review:** the current public Bronze CSV contains **130 distinct CN strings and 130 distinct `cn_id` values**, whereas the paper describes 129 CNs in its selected annotation pool. These figures refer to different descriptions of the resource and have not yet been reconciled with the original selection/export records. The published CSV has **not** been altered to force agreement. Users requiring an exact reconstruction of the original 129-CN selection should not infer it from these files without further provenance information.

The data contains potentially distressing hate speech. Handle raw text responsibly and consult the [main README](../README.md#ethics-and-limitations) for research limitations and usage conditions. No independent dataset license is declared in this repository.

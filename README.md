# FC-CONAN: An Exhaustively Paired Dataset for Robust Evaluation of Retrieval Systems

**FC-CONAN (Fully Connected CONAN)** is a research dataset for evaluating the retrieval of counter-narratives (CNs) in response to hate-speech (HS) messages. It addresses a central problem in counterspeech evaluation: an HS–CN pair missing from an existing dataset is not necessarily inappropriate. Treating unannotated pairs as negatives can substantially underestimate retrieval quality.

**Publication:** Short paper, *Second International Workshop on Next-Generation Language Models for Knowledge Representation and Reasoning (NeLaMKRR 2025)*, held in conjunction with KR 2025, pp. 63–69.

**Links:** [Paper on arXiv](https://arxiv.org/abs/2601.01350) · [Official proceedings (paper starts on p. 63)](https://jurisinformaticscenter.github.io/NeLaMKRR2025/proceedings.pdf#page=63) · [Workshop](https://jurisinformaticscenter.github.io/NeLaMKRR2025/)

> **Content warning:** The dataset contains English-language hate speech, including Islamophobic content. It is provided for research on counterspeech and retrieval evaluation; see [Ethics and limitations](#ethics-and-limitations).

## Overview and motivation

The original [CONAN dataset](https://aclanthology.org/P19-1271/) links hate-speech messages to expert-written counter-narratives, but it annotates only some possible pairings. This is a particular problem when a retrieval system recommends a suitable counter-narrative that happens not to be labeled as a match in the reference data.

FC-CONAN was designed to make this source of evaluation bias measurable. Starting from a selected English-language subset of CONAN, the study considered the full Cartesian product of **45 HS messages and 129 CNs (5,805 candidate pairs)**. Human annotation, adjudication, and independent validation produced four nested, quality-graded released partitions.

**Important distinction:** 5,805 is the number of candidate combinations considered in the annotation design, **not** the number of rows in each released CSV. After annotation and quality-control exclusions, the largest published partition (Bronze) contains **3,997 rows**.

**Release-provenance note:** The publicly released Bronze CSV contains **130 distinct CN identifiers/texts**, while the annotation design in the paper describes **129 selected CNs**. This difference has not yet been reconciled against the original selection/export artifacts; the data have not been altered. See the [partition documentation](fc_conan_partitions/README.md#methodological-scope-and-release-note).

The work contributes:

- A systematically paired, human-annotated HS–CN evaluation resource for a finite English-language subset.
- A two-stage annotation and validation procedure, with partitions that expose different quality–coverage trade-offs.
- An evaluation of how annotation sparsity affects counter-narrative retrieval metrics across twelve ranking approaches.
- Released annotation tables and retrieval-experiment input files that researchers can inspect and analyze independently.

This is a collaborative scientific contribution by the authors listed in the [citation](#citation), not an individually authored dataset.

## Dataset creation and validation

As described in Section 3 of the paper:

1. The study selected 45 English HS messages from CONAN and constructed a CN pool using semantically related counter-narratives retrieved with Sentence-BERT. From 133 distinct retrieved CNs, 129 were selected for the annotation design.
2. **Nine annotators** independently assessed the 5,805 candidate HS–CN combinations for appropriateness, with overlapping judgments and adjudication of disagreements. Uncertain or unresolved cases were excluded, leaving **5,032 adjudicated pairs**.
3. **Four independent validators** reviewed 4,000 of those pairs. Validators were allowed to skip content they found distressing; three pairs were skipped for this reason, leaving **3,997 records** in Bronze. All retained pairs were reviewed, whether or not a validator confirmed their original annotation. More selective partitions retain subsets according to agreement and validation criteria.

An *appropriate* label is a human judgment about a particular counter-narrative **for a particular HS message**. It is not an inherent property of the CN in isolation. The validation flag records whether the annotation was confirmed; it is distinct from the appropriateness label.

## Quality-graded partitions

The partitions are **nested**, not four independent train/test splits.

| Partition | Rows | Appropriate | Not appropriate | Selection rule |
| --- | ---: | ---: | ---: | --- |
| **Diamond** | 551 | 35 | 516 | At least two annotators, unanimous agreement, and validator confirmation |
| **Gold** | 663 | 54 | 609 | At least two annotators; adjudicated label confirmed by a validator |
| **Silver** | 3,580 | 431 | 3,149 | Includes Gold plus single-annotator judgments **confirmed by an independent validator** |
| **Bronze** | 3,997 | 702 | 3,295 | Includes Silver and judgments not confirmed during validation |

Counts are from Table 1 of the published paper. The quality criteria are described in Section 4.1. In particular, **Bronze includes 417 records not confirmed by validators**; these should not be confused with the 3,295 records labeled *not appropriate*.

Choose Diamond or Gold for stronger annotator-agreement criteria; Silver for more examples **all confirmed by a validator**; or Bronze for the broadest released coverage, while explicitly distinguishing the **3,580 confirmed** and **417 not-confirmed** records. Every Bronze record was assessed during validation.

## Repository structure

```text
FC-CONAN-dataset/
├── README.md
├── fc_conan_partitions/
│   ├── README.md
│   ├── diamond_df/diamond_partition_df.csv
│   ├── gold_df/gold_partition_df.csv
│   ├── silver_df/silver_partition_df.csv
│   └── bronze_df/bronze_partition_df.csv
└── recommender_experiment_data/
    ├── README.md
    ├── conan_not_in_bronze_train_df.csv
    ├── fc_conan_bronze_cn.csv
    └── fc_conan_bronze_hs.csv
```

- **Annotation partitions:** HS text, CN text, appropriateness decision, validator confirmation, source metadata, and individual annotator judgments. See the [partition field reference](fc_conan_partitions/README.md).
- **Recommender experiment data:** a CONAN-derived training-data file, a Bronze-derived candidate-CN catalogue, and a Bronze-derived HS query list. See the [experiment field reference](recommender_experiment_data/README.md).

**Column-name note:** The updated [partition field reference](fc_conan_partitions/README.md) lists the **literal CSV headers**, including `hate speech`, `counternarrative`, `Appropriate counternarrative (1) or non appropriate (0)`, and `Valid (1) / Non Valid (0) annotation`. The experiment input files use different text headers (`hateSpeech` and `counterSpeech`).

## Quick start: inspect the released data

No package installation is needed for a basic inspection. Clone or download this repository and, from its root, run the following command with Python 3:

```bash
python3 - <<'PY'
import csv
from pathlib import Path

label_column = "Appropriate counternarrative (1) or non appropriate (0)"
validation_column = "Valid (1) / Non Valid (0) annotation"

for name in ("diamond", "gold", "silver", "bronze"):
    path = Path("fc_conan_partitions") / f"{name}_df" / f"{name}_partition_df.csv"
    with path.open(encoding="utf-8", newline="") as handle:
        rows = list(csv.DictReader(handle))
    appropriate = sum(row[label_column] == "1" for row in rows)
    validator_confirmed = sum(row[validation_column] == "1" for row in rows)
    print(f"{name:7} rows={len(rows):4} appropriate={appropriate:3} validator_confirmed={validator_confirmed:4}")
PY
```

The `validator_confirmed` count indicates **agreement between the original annotation and an independent validator**; it does **not** count appropriate pairs. These values match Table 2 in the paper (551, 663, 3,580, and 3,580). Use `counternarrative` and `hate speech` as text fields in partition CSVs; consult the [partition field reference](fc_conan_partitions/README.md) for the full schema. This repository publishes the **data and experiment input splits**, not the complete code for rerunning the twelve reported ranking systems.

## Retrieval experiments: what the paper establishes

Section 4.3 evaluates **twelve retrieval/ranking approaches** using a fixed catalogue of **100 candidate CNs** and HS queries drawn from Bronze. The approaches include lexical methods (TF–IDF and BM25), Sentence-BERT variants, a dense embedding model, GPT-4o, embedding-plus-LLM reranking, and a random baseline. The evaluation compares top-10 rankings under the four FC-CONAN relevance partitions.

The paper reports Hit Ratio@10, MRR@10, NDCG@10, and MAP@10, among other measures. Its central observation is that **more complete relevance judgments reveal suitable recommendations missed by sparse labels**. For the specific candidate/query combinations used in the study, the original CONAN annotations alone mark no pairs as appropriate and therefore produce zero scores; this is **not** a general claim that every CONAN evaluation has zero performance.

Across the paper's reported comparisons, embedding-based rankers generally show stronger average scores and greater stability than the lexical baselines. Refer to **Figures 1–2 and Table 4 in the paper** for the reported values and their precise aggregation; the figures are not reproduced here. The three CSVs in `recommender_experiment_data/` are experiment inputs, **not saved model outputs or executable benchmark code**. The released 100-CN candidate catalogue does not include every CN appearing in the full Bronze CSV; see the [experiment input notes](recommender_experiment_data/README.md#separation-of-training-data-and-evaluation-inputs) for a file-level comparison.

## Ethics and limitations

- **Harmful language:** Files contain potentially distressing hate-speech material. Handle examples carefully; avoid reproducing raw hateful content in demonstrations, logs, or interfaces where it is unnecessary.
- **Research scope:** The work annotates selected English-language items, not the complete multilingual CONAN dataset (see the paper, §1 and §3).
- **Subjective labels:** Appropriateness depends on context and human judgment. Differences in annotator backgrounds and moderate initial agreement limit how literally any single label should be interpreted.
- **Quality levels:** Bronze retains records whose validation did not confirm their annotations. Prefer higher-confidence partitions when appropriate and report the partition used.
- **Dual use:** The data should not be repurposed to generate or amplify hate speech. An *appropriate* annotation describes a research judgment for an HS–CN pair; it is not a measurement of how people would respond to that counter-narrative in real-world settings.
- **Reproduction:** Published data and input splits do not by themselves reproduce the model evaluations, and reported results should not be generalized beyond the experimental protocol.

For detailed methodology, limitations, acknowledgments, and results, consult the published paper.

## Citation

If FC-CONAN is relevant to your research, please cite the **published workshop paper**:

```bibtex
@inproceedings{junqueras2025fcconan,
  title     = {{FC-CONAN}: An Exhaustively Paired Dataset for Robust Evaluation of Retrieval Systems},
  author    = {Junqueras, Juan and Boudin, Florian and Zin, May Myo and Nguyen, Ha Thanh and Fungwacharakorn, Wachara and Furman, Dami{\'a}n Ariel and Aizawa, Akiko and Satoh, Ken},
  booktitle = {Proceedings of the Second International Workshop on Next-Generation Language Models for Knowledge Representation and Reasoning (NeLaMKRR 2025)},
  year      = {2025},
  pages     = {63--69},
  url       = {https://jurisinformaticscenter.github.io/NeLaMKRR2025/proceedings.pdf#page=63}
}
```

The [arXiv version](https://arxiv.org/abs/2601.01350) is also available. The paper appeared in the **NeLaMKRR 2025 workshop proceedings**.

## License, usage, and attribution

**No explicit license file is currently included in this repository.** Public access to the CSV files should not be interpreted as an unrestricted license to copy, adapt, redistribute, or use the dataset commercially. The underlying material is derived from CONAN and may be subject to additional rights or conditions.

Please cite the FC-CONAN publication and the [original CONAN research](https://aclanthology.org/P19-1271/) as appropriate. Before redistribution or a use requiring explicit permissions, verify the applicable terms and contact the relevant rights holders/authors. The arXiv manuscript's distribution terms do **not** establish a license for these CSV files.

## Authors and acknowledgments

The research is a collaboration by **Juan Junqueras, Florian Boudin, May Myo Zin, Ha Thanh Nguyen, Wachara Fungwacharakorn, Damián Ariel Furman, Akiko Aizawa, and Ken Satoh**. Please see the paper for author affiliations, institutional support, and acknowledgments to the volunteer annotators and validators.

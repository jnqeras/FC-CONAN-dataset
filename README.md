# FC-CONAN Dataset

This repository hosts the partitions and experimental splits presented in FC-CONAN

### 1. Annotation Partitions
* **Diamond / Gold / Silver / Bronze** – annotated partitions located in:
  `fc_conan_partitions/diamond_df/`, `gold_df/`, `silver_df/`, `bronze_df/`

### 2. Recommender-System Experiment Splits
Located in `recommender_experiment_data/` and described in § 4.2 of the paper:
| File | Purpose |
|------|---------|
| `conan_not_in_bronze_train_df.csv` | Original CONAN dataset **minus** any HS or CN that appear in Bronze (training split). |
| `fc_conan_bronze_cn.csv`           | Candidate CNs from the Bronze partition (candidate pool). |
| `fc_conan_bronze_hs.csv`           | HS queries from the Bronze partition (query set). |

**Blind-review notice**  
A full citation, licence, and permanent DOI will be added
_if the paper is accepted_.

* Column dictionary for the annotation partitions: [`columns.md`](./fc_conan_partitions/README.md)  
* Column dictionary for the recommender splits: [`recommender_experiment_data/re_columns.md`](./recommender_experiment_data/README.md)

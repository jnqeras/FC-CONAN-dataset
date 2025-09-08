# Column Reference – Recommender-System Experiment Splits

The three CSV files in `recommender_experiment_data/` share names and formats
with the FC-CONAN partitions where possible.

---

## 1  conan_not_in_bronze_train_df.csv

| Column           | Type    | Description |
|------------------|---------|-------------|
| `cn_id`          | *int*   | Counter-narrative identifier from the original CONAN dataset. |
| `hateSpeech`     | *string*| HS text (equivalent to `hate_speech` in FC-CONAN). |
| `counterSpeech`  | *string*| CN text (equivalent to `counternarrative` in FC-CONAN). |
| `hsType`         | *string*| High-level hate-speech category. |
| `hsSubType`      | *string*| Fine-grained HS subtype. |
| `cnType`         | *string*| Counter-narrative category. |
| `age`            | *int*   | Age of the original CONAN HS annotator. |
| `gender`         | *string*| Gender of the original CONAN HS annotator. |
| `educationLevel` | *string*| Education level of the original CONAN HS annotator. |

---

## 2  fc_conan_bronze_cn.csv

| Column          | Type    | Description |
|-----------------|---------|-------------|
| `counterSpeech` | *string*| CN text (equivalent to `counternarrative` in FC-CONAN). |

---

## 3  fc_conan_bronze_hs.csv

| Column      | Type    | Description |
|-------------|---------|-------------|
| `hateSpeech`| *string*| HS text (equivalent to `hate_speech` in FC-CONAN). |

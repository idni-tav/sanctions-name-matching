# Financial Sanctions Name-Matching Analysis

## 1. Overview

Financial sanctions compliance is a critical high-risk area. Institutions must reliably identify sanctioned individuals, yet doing so incorrectly can have serious reputational, financial, and legal consequences.

Matching names across different sanctions lists is especially challenging. Entities may appear under multiple aliases, with spelling inconsistencies, transliteration differences, and language variations, making the datasets long, inconsistent, and difficult to reconcile.

This project focuses on developing and evaluating a high-precision name-matching pipeline that goes beyond standard fuzzy matching. It introduces a custom rule-based approach designed to handle variation-rich, multilingual, alias-heavy data, specifically targeting individual-level name matching between the UK and EU financial sanctions lists. This was achieved by using tokenization and heuristics.

In addition, a supervised learning refinement pipeline is used to evaluate the performance of the rule-based method and highlight areas for potential improvement.

---
## 2. Repository Structure

```
notebooks/
├── matching_pipeline/           # Rule-based mathcing vs Standard Fuzzy matching
│   ├── 01_clean_eu_sanctions.ipynb
│   ├── 02_clean_uk_sanctions.ipynb
│   ├── 03_match_names_standard.ipynb
│   ├── 04_match_names_grouped.ipynb
│   ├── 05_compare_matching_strategies.ipynb
│   └──README.md                 


├── ml_refinement_pipeline/      # Supervised learning evaluation
│   ├── 10_feature_engineering.ipynb
│   ├── 11_model_selection_and_training.ipynb
│   └──README.md                


data/
├── raw/                         # Original data sources
│   ├── ConList.csv
│   └── eu_condensed_List.csv

├── processed/                   # Intermediate data (auto-generated)

├── final/                       # Final outputs
│   ├── matched_names_grouped_method.csv
│   ├── matched_names_standard_method.csv
│   └── ml_label_predictions.csv
```

---
## 3. Installation & Setup
#### Step 1: Clone the repository

```bash
git clone https://github.com/idni-tav/sanctions-name-matching.git
cd sanctions-name-matching
```

#### Step 2: Download the raw data

Place the raw financial sanctions data files into the data/raw/ folder.
You will need to manually download the following publicly available files:

- UK Sanctions List

  - Visit the [UK Government Financial Sanctions](https://www.gov.uk/government/publications/financial-sanctions-consolidated-list-of-targets/consolidated-list-of-targets) website

  - Click the link to download the CSV version of the Consolidated List

  - Save the file as:
  ```bash
  data/raw/ConList.csv
  ```

- EU Sanctions List

  - Visit the [European Union Data](https://data.europa.eu/data/datasets/consolidated-list-of-persons-groups-and-entities-subject-to-eu-financial-sanctions?locale=en) website

  - Click the link to download the CSV version of the Consolidated Financial Sanctions File 1.1

  - Save the file as:
  ```bash
  data/raw/eu_condensed_list.csv
  ```

  
#### Step 3: Set up the Python environment 

```bash
conda env create -f environment.yml
```



---
## 4. How to use
The analysis is broken into several Jupyter notebooks, named in order from 01 to 11. To run them and reproduce the results, follow these steps:
- Activate conda environment:
```bash
conda activate eu_sanctions
```
- Launch jupyter notebook:

```bash
jupyter notebook
```
- In the browser window that pops up, open each notebook in numerical order:

  - 01_clean_eu_sanctions.ipynb
  - 02_clean_uk_sanctions.ipynb
  - (...)
  - 11_model_selection_and_training.ipynb


- For each notebook, run all cells. Use the menu option `Cell > Run All`or run cells one by one. This will generate all the output files and place them in the correct folders.

> Note: When cloning the repository, the final output .csv files are already included in `data/final/`. These will be re-generated when the notebooks are executed.

## 5. Key Results

- The rule-based grouped method produced high-precision results, with perfect precision and strong recall (0.92) on `not match` labels.
- The standard fuzzy-matching approach was more aggressive but showed lower precision and recall, highlighting the advantages of the custom grouped strategy in high-risk settings.
- A supervised learning pipeline was used to validate and refine the rule-based results. It began by training several models specifically for this task.
    - The best performing model was a tuned Random Forest, which was then used to predict labels on a holdout set — confirming the robustness of the training-phase predictions.
    - Feature importance analysis revealed that the most informative features were derived from the rule-based logic itself.
- The ML validation reinforced the strength of the grouped matching strategy and confirmed its ability to consistently outperform the baseline.
- It also offered insights for refinement, helping ensure that the best possible matches are consistently selected from a list of candidates.

- The ML validation reinforced the strength of the grouped matching strategy and confirmed its ability to consistently outperform the baseline.

For detailed results, see:
- [`notebooks/matching_pipeline/`](notebooks/matching_pipeline/)
- [`notebooks/ml_refinement_pipeline/`](notebooks/ml_refinement_pipeline/)

## 6. Limitations
- Overfitting to UK/EU context
  - The project focuses on matching individuals from the UK and EU sanctions lists, which have significant overlap due to shared geopolitical interests. This made them an ideal dataset for developing and validating the matching logic. However, it also means the approach may be highly tailored to this specific context and might not generalize well to other sanctions datasets with different structures or naming conventions.

- Subjectivity in manual validation
  - Several steps required human judgment to decide whether two names referred to the same person. While a set of consistent guidelines was applied to reduce bias, name interpretation remains inherently subjective. This is especially true in cases involving transliteration or cultural naming patterns, which could benefit from linguistic or regional expertise.

 
## 7. Future Work
- Incorporate language-specific heuristics
During manual review, it became clear that certain tokens (e.g. "Al", "Mohammed") are so common in some languages that they add little value for distinguishing names. The current approach used some length-weighting, but didn’t explicitly account for language frequency or cultural naming patterns. Future iterations could integrate heuristics for common name parts in languages like Arabic and Russian to reduce noise and improve results.

- Test generalizability across new datasets
Applying the matching logic to other datasets, such as those from OFAC, the UN, or commercial watchlists, would help test its robustness. It would also expose the method to a wider variety of naming formats, spelling irregularities, and transliteration issues, which could inform improvements to both rule-based and ML components.

For more specific improvements please consult the pipeline specific README.md files:
- [`notebooks/matching_pipeline/README.md`](notebooks/matching_pipeline/README.md)
- [`notebooks/ml_refinement_pipeline/README.md`](notebooks/ml_refinement_pipeline/README.md)

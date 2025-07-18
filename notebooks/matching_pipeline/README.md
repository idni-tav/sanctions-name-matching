# Matching Pipeline

## 1. Overview  
This pipeline implements and compares two different name matching strategies using the UK and EU official financial sanctions lists.

- **Standard method** – A baseline fuzzy matching approach.

- **Grouped method** – A custom rule-based strategy that adds tokenization and heuristic scoring to better capture variations and structure in names.

---

## 2. Pipeline Structure  

- `01_clean_eu_sanctions.ipynb` – Prepares and standardizes the EU sanctions data.  
  - Outputs:
    - `cleaned_eu_sanctions.pkl`  
    - `cleaned_eu_sanctions_grouped.pkl`  

- `02_clean_uk_sanctions.ipynb` – Prepares and standardizes the UK sanctions data.  
  - Outputs:
    - `cleaned_uk_sanctions.pkl`  
    - `cleaned_uk_sanctions_grouped.pkl`  

- `03_match_names_standard.ipynb` – Applies the standard fuzzy matching method.  
  - Outputs:
    - `matched_standard.pkl`  

- `04_match_names_grouped.ipynb` – Applies the grouped rule-based method.  
  - Outputs:
    - `matched_grouped.pkl`  

- `05_compare_matching_strategies.ipynb` – Evaluates and compares both approaches.  
  - Outputs (for use in the ML refinement pipeline):
    - `automation_data.pkl`  
    - `automated_sample_labeled.pkl`  

---

## 3. Methodology Summary  

- Each sanctions list was individually cleaned and standardized to ensure compatibility with the matching logic.

- Each approach was implemented separately, assigning the most likely EU counterpart, to each UK name.

- Each resulting UK–EU name pair was labeled as:

  - `match` – high-confidence match

  - `not match` – clear non-match

  - `preliminary match` – ambiguous; flagged for human review

- The two methods were evaluated using two complementary strategies:

  - **Label Quality** – manual review of a random sample of 250 name pairs to calculate precision (`match` / `not match`) and recall (`not match`)

  - **Best Match Selection** – manual evaluation of 60 disagreement cases where each method selected a different best match
---

## 4. Key Results  

- The grouped method achieved perfect precision for both match and not match labels, with a not match recall of 0.91, reflecting its conservative, high-precision design.

- The standard method showed slightly lower match precision (0.87) and weaker not match recall (0.58), consistent with a more aggressive but riskier matching style.

- The methods disagreed on the best match in 16% of cases. About half of these involved equally plausible alternatives.

- In the remaining cases, no method was consistently better, but the cases in which the standard method outperformed the grouped method exposed specific limitations in the grouped method’s thresholding and scoring rules.

Overall, the grouped method is more nuanced, conservative, and precise, better aligning with the high-risk nature of the task. However, it occasionally misses good matches due to rule-based blind spots.


---

## 5. Outputs  

**Final Outputs:**

- `matched_names_standard_method.csv` – UK-EU match results and labels using the standard method.  
- `matched_names_grouped_method.csv` – UK-EU match results and labels using the grouped method.  

**Downstream Data for ML Pipeline:**

- `automation_data.pkl` – Cleaned pairwise features and rule-based labels.  
- `automated_sample_labeled.pkl` – Subset of data manually labeled for model training/testing.  

---

## 6. Key Limitations  

- **Sample Size** – While the UK dataset included 3,608 individuals, only 250 and 60 examples were manually reviewed for label and match quality. This limits statistical robustness.
- **Task Subjectivity** – Name matching is inherently ambiguous, especially across transliterations and naming conventions. While human evaluators followed consistent guidelines to maintain objectivity, some degree of bias is unavoidable.

---

## 7. Future Improvements  

- Address blind spots in the grouped method by tuning thresholding and scoring rules, particularly lowering similarity thresholds to avoid filtering out viable matches prematurely.

 


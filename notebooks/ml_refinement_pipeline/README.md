# ML Refinement Pipeline 

## 1. Overview  

This pipeline evaluates whether matched name pairs can be accurately classified as `match`, `not match`, or `preliminary match` using supervised machine learning models.

The goal is to assess the predictive power of existing features and explore whether model-driven tuning can complement or improve the rule-based matching logic developed in the `matching_pipeline`.

(For full implementation details, experimental results, and evaluation breakdowns, please refer to the accompanying notebooks.)


## 2. Pipeline Structure  

- `10_feature_engineering.ipynb` – Extracts relevant features and prepares the dataset for training.
- Outputs:
    - `features_data.pkl`  
    - `features_sample_labeled.pkl`  

- `11_model_selection_and_training.ipynb` – Trains multiple models and generates label predictions using the best-performing one.
  - Outputs:
    - `ml_label_predictions.csv`


---

## 3. Methodology Summary  

- The same core features used in the rule-based matching logic were reused to create a structured dataset for model training.

- Six experiments were conducted using models such as Logistic Regression, Decision Trees, Random Forests, and XGBoost.

- Models were evaluated based on:

  - **Precision** for `match` and `not match`

  - **Recall** for `not match`

  - Overall weighted score of the previous metrics

- The best-performing model was selected to label a separate holdout dataset and validate its generalization.

- Feature importance analysis was then conducted on this model to explore opportunities for improving the existing rule-based system.


---

## 4. Key Results  

- **Top-performing models**: The best results came from Model 3 (Tuned Random Forest) and Model 6 (XGBoost), with weighted scores of 0.973 and 0.976, respectively.

- **Model selection**: While XGBoost scored slightly higher overall, it showed more variance across folds. Random Forest offered a better balance of precision, stability, and alignment with the project’s goals, and was therefore selected as the final model.

- **Holdout validation**: When applied to a separate holdout set, the Random Forest model produced consistent results (weighted score ~0.97), confirming the model’s robustness. Precision remained high, with only a minor drop in recall.

- **Comparison to rule-based method**: As expected, ML models did not significantly outperform the rule-based approach. This is likely because the input features were already composite indicators derived from the matching logic, limiting the models' ability to learn new decision boundaries.

- **Feature importance**: The model relied heavily on `multi_score` (≈50% of decision weight), confirming the strength of this metric. `coverage_ratio` contributed ≈25%, suggesting that slightly increasing its weight in the rule-based formula could improve performance.

---

## 5. Outputs  

**Final Outputs:**

- `ml_label_predictions.csv` – Predicted labels on the holdout set for all UK–EU name pairs.


---

## 6. Key Limitations  

- Sample Size Constraints: The full dataset included 3,608 individuals, but manual evaluations were limited to smaller samples of 250 and 60 pairs due to resource constraints. These small sample sizes reduce the statistical robustness of the validation.

---

## 7. Future Improvements  

- Increase the weight of `coverage_ratio` in the rule-based `multi_score` to better reflect its importance as learned by the model.

- For this analysis, features were intentionally engineered to validate the `multi_score`, meaning they encoded existing logic. Future work could explore more granular or decomposed features to allow models to uncover new patterns.



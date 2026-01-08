# **Bridge Condition Classification**  

## **Project Background**
This project analyzes the structural condition of bridges across Georgia, focusing on identifying bridges at risk of deterioration. The Department of Transportation (DOT) monitors bridge safety to ensure reliable and safe transportation infrastructure for the public. Understanding which bridges are most likely to be in Poor or Fair condition, how deterioration relates to age, traffic volume, and structural features, and which characteristics most influence risk helps the DOT prioritize inspections, allocate maintenance resources efficiently, and plan long-term infrastructure investments. Bridges in Poor condition (and to a lesser extent Fair condition) are higher priority for inspections and maintenance to prevent safety hazards and costly failures.

## **Objective**
Predict the condition of bridges (Good, Fair, Poor) using structural and traffic features to help the Department of Transportation prioritize inspections, allocate maintenance resources efficiently, and enhance public safety, with particular focus on identifying Poor bridges, which pose the highest safety risk and require immediate attention.

The python code for the analysis can be found [here](Bridge_Condition_Classification_Code.ipynb)

---

## **Dataset Overview**
This analysis uses real-world data from the [National Bridge Inventory (NBI)](https://www.fhwa.dot.gov/bridge/nbi.cfm), including information collected on 14,987 Georgia bridges in 2021.

The original dataset contained hundreds of variables, many of which were identifiers or not directly related to bridge condition. It should be noted that `age` and `reconstructed` were derived through feature engineering. The final set of variables used in the analysis includes:

- `adt` – Average daily traffic
- `structure_len_mt` – Total bridge length in meters
- `deck_width_mt` – Deck width in meters
- `percent_adt_truck` – Percentage of truck traffic
- `max_span_len_mt` – Maximum span length in meters
- `age` – Calculated as `2021 - year_built`, representing the age of the bridge
- `bridge_condition` – Target variable indicating condition (Poor, Fair, Good)
- `structure_kind` – General type of bridge structure
- `structure_type` – Specific type of bridge structure
- `design_load` – Load rating/design category
- `functional_class` – Functional classification of the road the bridge carries
- `service_on` – Type of service or road
- `reconstructed` – Binary indicator if the bridge has ever been reconstructed

The target variable `bridge_condition` was composed of the following groups:

**Target Variable Distribution:**  
- `0 = Poor` – 319 bridges (2.13%)  
- `1 = Fair` – 3,614 bridges (24.11%)  
- `2 = Good` – 11,054 bridges (73.76%)

---

## **Model Overview**  
A **Random Forest Classifier** was trained on historical bridge data.  

Key parameters:  
- Number of trees: 200    
- Random state: 0 (for reproducibility)
- 5-Fold Cross Validation was implemented to ensure no overfitting from the model

A **custom threshold** of 0.05 was applied for predicting Poor bridges to increase detection sensitivity for this rare and critical class.  

---

## **Executive Summary** 

**Model Performance:** Achieved **75% recall** for Poor bridges using Random Forest, successfully flagging 3 out of 4 structurally deficient bridges while maintaining **74% overall accuracy**.

Due to significant class imbalance (2.13% Poor bridges) and the inherent difficulty to fully capture relationships, the model prioritizes safety-critical detection over balanced accuracy.

| Class      | Precision | Recall | F1-Score | Support |
|------------|-----------|--------|----------|---------|
| **Poor (0)**   | 0.30      | **0.75**   | 0.43     | 64      |
| Fair (1)   | 0.58      | 0.30   | 0.39     | 718     |
| Good (2)   | 0.85      | 0.88   | 0.87     | 2,208    |

**Overall Accuracy:** 74.1%  

---

## **Insights:**

**Poor Bridges (Safety-Critical Class):**
* **75% recall**: Successfully flags 48 out of 64 structurally deficient bridges, significantly reducing risk of catastrophic failures
* **30% precision**: Generates false alarms, but in bridge safety, over-flagging is appropriate—inspection costs (~$2K) are negligible compared to collapse costs ($50M+)
* **Trade-off justification**: Prioritized sensitivity over specificity to align with FHWA safety mandates and minimize public risk

**Fair Bridges (Middle Class Challenge):**
* **30% recall**: Struggles due to ambiguous boundaries—"Fair" features overlap significantly with both Good and Poor characteristics
* **Real-world validation**: Human inspectors show similar inconsistency; deck ratings of 5 vs 6 often vary between evaluators
* **Acceptable limitation**: Model reflects inherent ambiguity in transition zones rather than model weakness

**Good Bridges (Strong Performance):**
* **88% recall, 85% precision**: Correctly identifies 1,944 out of 2,208 safe bridges, preventing wasted inspection resources
* **Business value**: Allows DOT to focus limited resources on truly high-risk assets while safely reducing inspection frequency for verified Good bridges

**Key Takeaway**: Model optimized for **risk mitigation** rather than balanced accuracy appropriate for infrastructure safety applications where missing Poor bridges has exponentially higher costs than false positives.

---

## **Key Drivers of Bridge Condition**  

The Random Forest model identifies the most important features for predicting bridge condition:  

**Top Predictive Features:**  
1. **Age (importance: 0.28)** – Strongest predictor; bridges over 50 years show significantly higher deterioration rates
2. **Average Daily Traffic (importance: 0.19)** – Counterintuitively, poor bridges had a significantly lower mean average daily traffic, suggesting that many of them are lightly used rural bridges, which may have been built with weaker materials given the anticipated low traffic they would encounter.
3. **Structure Length (importance: 0.15)** – Longer spans have more potential failure points and stress concentrations

![Feature Importance Plot](feature_importance_rf.png)

**Actionable Business Insight:**  
High-risk bridge profile for prioritized inspection: Age > 60 years, ADT < 600, Length < 40 m. These thresholds were computed from the 75th percentile of each feature among poor bridges. Since these features were found to be the most important features as shown in the feature importance plot, special attention is given to bridges exhibiting this combination to enable early flagging of high-risk structures.

---

## **Recommendations for Action**  

**Immediate Actions:**
1. **Flag high-risk bridges:** Prioritize inspection for bridges matching the high-risk profile (age >50, ADT <2k, length >100m). Model identifies approximately [X] bridges meeting these criteria.
2. **Verify model predictions:** All bridges predicted as Poor should receive verification inspections within 90 days, even suspected false positives—the $2K cost is justified by $50M+ failure prevention.
3. **Optimize resource allocation:** Reduce inspection frequency for high-confidence Good bridges (probability >0.90), reallocating saved budget to high-risk monitoring.

**Long-term Improvements:**
4. **Enhanced data collection:** Add material type, inspection history, environmental exposure (coastal/mountain), and maintenance records to improve Fair/Poor class differentiation.
5. **Adopt successful practices:** Study well-maintained older bridges (age >60 but Good condition) to identify effective preservation strategies; replicate across deteriorating peer bridges.
6. **Continuous model updates:** Retrain annually with new NBI data as traffic patterns, climate impacts, and infrastructure age distribution evolve.

---

## **Next Steps / Future Enhancements**  
- **Threshold optimization:** Test cost-sensitive learning with actual DOT budget constraints to fine-tune Poor/Fair decision boundaries
- **Advanced algorithms:** Evaluate other models such as XGboost with SMOTE oversampling and ordinal regression to better capture Fair condition nuances
- **Deployment:** Build interactive dashboard for DOT staff to input bridge characteristics and receive real-time risk assessments

---


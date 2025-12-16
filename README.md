# **Bridge Condition Classification – Executive Summary**  

## **Project Objective**  
The goal of this project is to predict the **structural condition of bridges** across the state using key bridge and traffic characteristics. Accurate predictions allow infrastructure teams to **prioritize inspections and maintenance**, optimize resources, and **reduce safety risks**.  

**Target Variable:**  
- `0 = Poor`  
- `1 = Fair`  
- `2 = Good`  

**Input Features:** Structural and traffic-related metrics, such as age, average daily traffic (ADT), bridge length, design load, and bridge type.  

---

## **Model Overview**  
A **Random Forest Classifier** was trained on historical bridge data (~3,000 bridges).  
Key parameters:  
- Number of trees: 200  
- Class weights: balanced manually to give equal weight to Poor, Fair, and Good bridges  
- Random state: 0 (for reproducibility)  

A **custom threshold** of 0.05 was applied for predicting Poor bridges to increase detection sensitivity for this rare and critical class.  

---

## **Final Model Performance (Test Set)**  

| Class      | Precision | Recall | F1-Score | Support |
|------------|-----------|--------|----------|---------|
| Poor (0)   | 0.30      | 0.75   | 0.25     | 64      |
| Fair (1)   | 0.58      | 0.30   | 0.39     | 718     |
| Good (2)   | 0.85      | 0.88   | 0.87     | 2208    |

**Overall Accuracy:** 0.741  

**Interpretation:**  
- **Poor bridges:** The model correctly identifies **75% of all actual Poor bridges** (high recall), but **many bridges flagged as Poor are actually Fair or Good** (precision 30%). This is acceptable for safety-focused monitoring, where catching most Poor bridges is critical.  
- **Fair bridges:** Hardest to predict accurately; likely overlaps with Good/Poor features.  
- **Good bridges:** Predictions are very accurate, with high precision and recall.  

---

## **Key Drivers of Bridge Condition**  

The Random Forest model highlights the most important features influencing bridge condition:  

**Top Features:**  
1. **Age** – Older bridges are more likely to be Poor or Fair.  
2. **Average Daily Traffic (ADT)** – Bridges with higher traffic volumes tend to show more wear.  
3. **Bridge Length** – Longer spans are associated with higher structural risk.  

*Insert Feature Importance Plot Here*  

**Business Insight:**  
- Age, traffic, and structural dimensions are the primary drivers of bridge condition.  
- These features can guide targeted inspections and maintenance prioritization.  

---

## **Recommendations for Action**  
1. **Prioritize Poor bridges:** Even with moderate precision, high recall ensures most critical bridges are flagged for inspection.  
2. **Further data collection:** Enhance features like material type, inspection history, and environmental factors to improve Fair class prediction.  
3. **Integrate into planning:** Use model outputs to allocate maintenance resources efficiently and reduce safety risk.  
4. **Continuous monitoring:** Update the model periodically with new inspections and traffic data to maintain accuracy.  

---

## **Next Steps / Future Enhancements**  
- Explore **permutation-based feature importance** to validate which features most influence test-set predictions.  
- Test **threshold optimization** or **cost-sensitive learning** to balance recall vs precision for Poor bridges.  
- Investigate **additional models** (XGBoost, LightGBM) for potential performance gains.  
- Implement a **dashboard**


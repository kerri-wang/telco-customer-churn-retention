# Telco Customer Churn Retention Strategy

An end-to-end business analytics project that uses customer segmentation, leakage-free churn prediction, and integer programming to optimize telecom retention outreach under a fixed budget.

This project was developed for **Business Value Through Integrative Analytics** at **Carnegie Mellon University Tepper School of Business**.

---

## Project Overview

Customer churn is one of the most important profitability challenges for telecom providers. Instead of only predicting which customers are likely to churn, this project connects churn risk to customer lifetime value, intervention cost, and operational constraints.

The project answers three business questions:

1. **Which customers are most likely to churn?**
2. **Which customer segments create the highest revenue exposure?**
3. **How should a limited retention budget be allocated across different outreach tiers?**

The final recommendation is to deploy a **three-tier retention program** that prioritizes high-risk, high-CLTV customers using an integer programming optimization model.

---

## Executive Summary

| Area | Result |
|---|---:|
| Dataset | 7,043 customer records |
| Original features | 54 |
| Overall churn rate | 26.5% |
| Segmentation method | K-Means clustering |
| Final customer segments | 4 |
| Final model | Leakage-free Random Forest |
| Corrected model AUC | ~0.90 |
| Optimization method | Integer Programming |
| Optimization package | PuLP |
| Budget scenario | $7,500 |
| Intervention tiers | Email/SMS, Phone Outreach, Premium Package |
| Final business lift | +26% vs. random targeting |

---

## Business Problem

Telecom companies often have limited retention resources. A retention team cannot contact every customer, and different outreach methods have different costs and expected success rates.

A simple churn model can rank customers by risk, but it does not answer the full business question:

> Given a fixed budget, which customers should receive which retention action?

This project builds a decision framework that combines:

- predicted churn probability
- customer lifetime value
- intervention cost
- expected retention effectiveness
- budget and contact constraints

---

## Repository Structure

```text
telco-customer-churn-retention/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── data/
│   ├── Telco_Customer_Churn_Full_33.xlsx
│   └── data_dictionary.md
│
├── notebooks/
│   ├── 01_segmentation_pca_revenue_at_risk.ipynb
│   ├── 02_predictive_modeling_intervention_value.ipynb
│   └── 03_leakage_free_optimization_final.ipynb
│
├── reports/
│   ├── update1_segmentation_presentation.pdf
│   ├── update2_predictive_modeling_presentation.pdf
│   └── final_telco_churn_retention_presentation.pdf
│
├── outputs/
│   ├── figures/
│   └── tables/
│
└── assets/
    └── readme_images/
```

---

## Project Evolution

This project was completed in three phases.

### Update 1: Customer Segmentation & Revenue at Risk

The first phase focused on descriptive and diagnostic analytics.

Key work completed:

- Cleaned and prepared customer-level telecom data
- Explored churn patterns by contract type, tenure, internet type, and engagement
- Engineered business-relevant features
- Built customer segments using K-Means clustering
- Used PCA to help validate and visualize the segmentation structure
- Estimated revenue at risk by customer segment

Key features engineered:

| Feature | Definition | Business Rationale |
|---|---|---|
| `service_bundle_count` | Sum of selected service flags | Measures product depth and switching cost |
| `tenure_segment` | New, developing, or established customer | Captures non-linear churn behavior by tenure |
| `engagement_index` | Standardized combination of referrals, bundle count, and tenure | Measures customer loyalty and engagement |

Initial customer segments:

| Segment | Business Interpretation |
|---|---|
| Early High-Risk | Newer customers with high churn exposure |
| High-Value Bundled | High-CLTV customers with multiple services |
| Loyal Advocate | Low-churn customers with strong loyalty signals |
| Stable Low-Usage | Lower-usage customers with moderate stability |

---

### Update 2: Predictive Modeling & Intervention Value

The second phase extended the project from segmentation to predictive modeling and retention targeting.

Key work completed:

- Trained multiple churn classification models
- Compared Logistic Regression, Decision Tree, Random Forest, and XGBoost
- Evaluated model lift against random targeting
- Introduced an intervention value framework
- Compared simple retention targeting heuristics

The intervention value formula was:

```text
v_ij = p_churn_i × retention_rate_j × CLTV_i − cost_j
```

Where:

| Term | Meaning |
|---|---|
| `p_churn_i` | Predicted churn probability for customer i |
| `retention_rate_j` | Expected effectiveness of intervention tier j |
| `CLTV_i` | Customer lifetime value for customer i |
| `cost_j` | Cost of intervention tier j |
| `v_ij` | Expected net value of assigning customer i to tier j |

This phase showed that retention strategy should not be based on churn probability alone. The best targeting decisions also depend on customer value and intervention cost.

---

### Update 3: Leakage-Free Modeling & Prescriptive Optimization

The final phase corrected signal leakage and moved from heuristic targeting to prescriptive optimization.

A leakage audit found that `satisfaction_score` was strongly correlated with churn and behaved like a near-direct proxy for the outcome. It was removed from all final predictive models. Other post-outcome fields were also excluded.

Excluded leakage-prone fields:

```text
satisfaction_score
churn_score
churn_label
customer_status
churn_category
churn_reason
churn_value
```

Final Update 3 work completed:

- Removed leakage-prone features from predictive modeling
- Rebuilt clusters without `satisfaction_score`
- Trained leakage-free churn models
- Selected Random Forest as the primary scoring model
- Evaluated models using business value, not AUC alone
- Built an integer programming model for customer-to-tier assignment
- Added budget, contact, and safety constraints
- Compared the optimal solution against heuristic strategies
- Added risk-adjusted and sensitivity analysis

---

## Methodology

### 1. Data Preparation

The data preparation workflow included:

- standardizing column names
- converting data types
- encoding binary service variables
- handling missing values
- creating engineered features
- separating safe predictors from post-outcome leakage fields

The final modeling dataset used customer attributes that would be observable before churn.

---

### 2. Customer Segmentation

K-Means clustering was used to identify customer groups with distinct churn and value profiles.

Final clustering features included:

```text
tenure_in_months
monthly_charge
cltv
number_of_referrals
service_bundle_count
engagement_index
```

The final model used 4 customer segments:

| Segment | Description |
|---|---|
| Early High-Risk | Newer customers with high churn probability and lower engagement |
| High-Value Bundled | Long-tenure customers with high CLTV and deeper product adoption |
| Stable Low-Usage | Lower-usage customers with moderate retention risk |
| Loyal Advocate | Low-risk customers with stronger loyalty and referral behavior |

---

### 3. Leakage-Free Churn Prediction

The final predictive model excluded features that directly or indirectly revealed churn outcomes.

Final model candidates included:

- Logistic Regression
- Decision Tree
- Random Forest

The final selected model was:

> **Random Forest Classifier**

Final model performance:

| Model | AUC | Average Precision |
|---|---:|---:|
| Logistic Regression | 0.8971 | 0.7336 |
| Decision Tree | 0.8792 | 0.6997 |
| Random Forest | 0.8986 | 0.7442 |

The corrected AUC of approximately 0.90 is more realistic than the earlier inflated AUC caused by signal leakage.

Top Random Forest features:

| Rank | Feature |
|---:|---|
| 1 | Contract |
| 2 | Engagement Index |
| 3 | Number of Referrals |
| 4 | Monthly Charge |
| 5 | Tenure in Months |

These features are interpretable and align with the business hypothesis that contract structure, engagement, tenure, and price sensitivity are major churn drivers.

---

## Optimization Model

The final stage translates churn predictions into retention actions.

Each customer can receive one of three intervention tiers or no intervention.

| Tier | Intervention | Cost | Assumed Retention Rate |
|---:|---|---:|---:|
| 1 | Email / SMS | $5 | 10% |
| 2 | Phone Outreach | $15 | 25% |
| 3 | Premium Package | $50 | 45% |

### Decision Variable

```text
x_ij = 1 if customer i is assigned to intervention tier j
x_ij = 0 otherwise
```

### Objective Function

```text
Maximize total expected retention value:

sum_i sum_j x_ij × v_ij
```

Where:

```text
v_ij = p_churn_i × retention_rate_j × CLTV_i − cost_j
```

### Constraints

The optimization model includes:

1. **Budget constraint**

```text
Total intervention cost ≤ $7,500
```

2. **One-tier-per-customer constraint**

```text
Each customer can receive at most one intervention
```

3. **Safety constraint**

```text
Customers with p_churn > 0.80 must be contacted
```

4. **Binary assignment constraint**

```text
x_ij ∈ {0, 1}
```

---

## Business KPI Evaluation

Instead of selecting a model based only on AUC, the final evaluation compares strategies based on expected retention value.

Strategies compared:

| Strategy | Description |
|---|---|
| Random targeting | Randomly select customers |
| Top churn probability | Target customers with highest predicted churn |
| Top revenue at risk | Target high-risk and high-CLTV customers |
| Top intervention value | Target customers with highest positive expected value |
| Integer programming | Optimize tier assignment under budget and contact constraints |

Final presentation result:

> The integer programming solution outperformed random targeting by approximately **26%** in expected retention value.

---

## Final Recommendation

Deploy an IP-driven three-tier retention program.

### Recommended Strategy

| Customer Type | Recommended Action |
|---|---|
| High churn probability + high CLTV | Prioritize for premium or phone outreach |
| High churn probability + moderate CLTV | Use phone outreach or email/SMS depending on value |
| Moderate churn probability + positive intervention value | Use low-cost email/SMS |
| Low churn probability or negative expected value | Do not contact immediately |

---

## 90-Day Deployment Plan

### Days 1–30: Deploy & Score

- Operationalize the churn scoring pipeline
- Score customers monthly
- Run the integer programming solver under the current budget
- Launch a pilot email/SMS campaign for high-risk customers
- Track contact rate, response rate, and early retention outcomes

### Days 31–60: Expand & Validate

- Expand phone outreach for medium- and high-value churn risks
- Compare actual retention outcomes against assumed retention rates
- Recalibrate intervention effectiveness assumptions
- Monitor model performance by customer segment

### Days 61–90: Optimize & Scale

- Launch premium offers for top-CLTV churners
- Automate monthly optimization
- Build an ROI dashboard for leadership
- Monitor model drift and campaign performance
- Update budget allocation rules based on real campaign outcomes

---

## Key Takeaways

1. **High AUC is not enough.**  
   A model can look strong technically but still fail to support business decisions if it contains leakage or ignores intervention costs.

2. **Leakage detection matters.**  
   Removing `satisfaction_score` and other post-outcome fields made the model more realistic and deployable.

3. **Churn probability should be combined with CLTV.**  
   The best retention targets are not always the customers with the highest churn risk. Customer value also matters.

4. **Optimization improves targeting.**  
   Integer programming provides a structured way to assign customers to retention actions under a fixed budget.

5. **Retention strategy should be tested.**  
   Retention rates used in the model are assumptions and should be validated through A/B testing or pilot campaigns.

---

## Tools & Technologies

| Category | Tools |
|---|---|
| Programming | Python |
| Data Analysis | pandas, NumPy |
| Visualization | matplotlib, seaborn |
| Machine Learning | scikit-learn |
| Optimization | PuLP |
| Modeling | Logistic Regression, Decision Tree, Random Forest |
| Notebook Environment | Jupyter Notebook, Google Colab |
| Version Control | Git, GitHub |

---

## How to Run This Project

### 1. Clone the repository

```bash
git clone https://github.com/kerri-wang/telco-customer-churn-retention.git
cd telco-customer-churn-retention
```

### 2. Create a virtual environment

```bash
python -m venv venv
source venv/bin/activate
```

For Windows:

```bash
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Add the dataset

Place the dataset in the `data/` folder:

```text
data/Telco_Customer_Churn_Full_33.xlsx
```

### 5. Run the notebooks

Run the notebooks in order:

```text
01_segmentation_pca_revenue_at_risk.ipynb
02_predictive_modeling_intervention_value.ipynb
03_leakage_free_optimization_final.ipynb
```

---

## Suggested Requirements File

```text
pandas
numpy
matplotlib
seaborn
scikit-learn
pulp
openpyxl
jupyter
```

If using XGBoost in the Update 2 notebook:

```text
xgboost
```

---

## Limitations

This project is designed as an analytics case study. Several assumptions should be validated before real-world deployment:

- Retention success rates are assumed, not experimentally measured.
- The model is predictive, not causal.
- CLTV is treated as an input and not independently modeled.
- The dataset is historical and may not reflect future customer behavior.
- A production system would require monitoring for model drift.
- Real deployment should use A/B testing to estimate actual treatment effects.

---

## Future Improvements

Potential extensions include:

- Add a Streamlit dashboard for interactive churn scoring and budget optimization
- Build a Tableau or Power BI dashboard for executive reporting
- Add SHAP values for model explainability
- Estimate treatment effects using uplift modeling
- Add customer-level recommendation logic
- Automate monthly scoring and optimization workflow
- Add model monitoring and drift detection

---

## Project Deliverables

| Deliverable | Description |
|---|---|
| Segmentation notebook | Customer clustering, PCA, and revenue-at-risk analysis |
| Predictive modeling notebook | Churn models and intervention value framework |
| Final optimization notebook | Leakage-free model and integer programming solution |
| Presentation deck | Executive-ready summary of methodology and recommendation |
| README | Recruiter-friendly project overview and documentation |

---

## Business Takeaway

Retention strategy should not be based on churn probability alone. The most effective approach combines churn risk, customer lifetime value, intervention cost, and operational constraints.

This project shows how a telecom provider can move from descriptive churn analysis to a practical, budget-aware retention decision system.

# 📊 User-Level A/B Test Analysis (R + SQL + Power BI)

This project presents an end-to-end **user-level A/B test analysis workflow**, focusing on **metric definition, statistical testing, and business interpretation**.

The analysis is conducted primarily in **R**, with:
- **SQL** used to illustrate how metrics would be extracted in a production data environment
- **Power BI** used for exploratory validation and stakeholder-facing visualization

The project emphasizes **experimental rigor, statistical correctness, and business-readability**, rather than model complexity.

---

## 🎯 Project Objective

The goal of this project is to evaluate whether a product variant leads to a **statistically and practically meaningful improvement** over a control group.

Specifically, the analysis aims to:

- Validate **randomization quality** (SRM sanity check)
- Compare **conversion rate** between variants (primary metric)
- Evaluate **monetization impact** via ARPU (secondary metric)
- Quantify **effect sizes (lift)** for business interpretation
- Present results in a **clear, decision-oriented format**

---

## 🧪 Experiment Design

- **Unit of randomization**: User-level  
- **Groups**:
  - `control`
  - `variant`
- **Observation window**: Fixed (as provided by dataset)
- **Analysis granularity**: One row per user

Each user is exposed to **exactly one variant**, satisfying the assumptions required for classical A/B testing.

---

## 📌 Metrics Definition

### **Primary Metric**
- **Conversion Rate**
  - Defined as the proportion of users who converted
  - Binary outcome (`converted ∈ {0,1}`)
  - Tested using a **two-sample proportion test**

### **Secondary Metrics**
- **ARPU (Average Revenue Per User)**
  - Includes both converted and non-converted users
  - Tested using **Welch’s t-test** due to skewed distribution

- **Revenue per Converted User** (descriptive)
  - Used for contextual understanding, not formal hypothesis testing

---

## 📊 Statistical Methodology (R)

All statistical analysis is performed in **R**.

### Methods Used:
- Two-sample proportion test (conversion rate)
- Welch two-sample t-test (ARPU)
- 95% confidence intervals
- Absolute and relative lift calculations

### Key Considerations:
- No assumption of normality for revenue
- User-level independence
- Explicit separation of **statistical significance** vs **business significance**

---

## 📈 Power BI Visualization

Power BI is used for **sanity checks and stakeholder-friendly validation**, including:

1. **User count by variant**  
   - Visual SRM check (balanced traffic allocation)

2. **Conversion rate by variant**  
   - Aggregated mean of binary conversion indicator

3. **Average revenue per user (ARPU)**  
   - Highlights monetization differences across groups

These visuals are **not used for inference**, but to support interpretability and communication.

---

## 🗄️ Metric Extraction via SQL (Illustrative Example)

In a production environment, user-level metrics are typically extracted from a data warehouse using SQL **before** being analyzed in R.

The following example demonstrates how the A/B test metrics in this project could be constructed.

```sql
WITH user_level AS (
    SELECT
        variant_name,
        user_id,
        MAX(converted) AS converted,
        SUM(revenue) AS revenue
    FROM ab_test_results
    GROUP BY variant_name, user_id
),
metrics AS (
    SELECT
        variant_name,
        COUNT(*) AS n_users,
        SUM(converted) AS conversions,
        AVG(converted) AS conversion_rate,
        AVG(revenue) AS arpu
    FROM user_level
    GROUP BY variant_name
)
SELECT *
FROM metrics;
```
This logic mirrors the user-level aggregation applied in the R analysis and reflects standard analytics practice in industry environments.

## 📂 Repository Structure

```text
ab-test-user-level-analysis/
│
├── data/
│   └── ab_test_user_level.csv
│
├── analysis/
│   └── ab_test_analysis.Rmd
│
├── powerbi/
│   └── ab_test_dashboard.pbix
│
└── README.md
```

## 📌 Key Takeaways

The control group outperformed the variant in both conversion rate and ARPU.

Neither difference was statistically significant at the 5% level.

Effect size estimates suggest no practical uplift.

Recommendation: Do not roll out the variant.

## 🧠 Skills Demonstrated

A/B test design and evaluation

Metric definition and validation

Statistical testing in R

SQL-based analytical thinking

Business-oriented result communication

Power BI dashboard construction

## ⚠️ Notes

This project focuses on methodology, not model building.

SQL examples are illustrative and not tied to a specific database engine.

Power BI is used for visualization only, not inference.

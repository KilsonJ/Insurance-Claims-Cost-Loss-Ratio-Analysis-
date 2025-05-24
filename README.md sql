# **Insurance Fraud Detection Analysis**

> The dataset contains 1,000 insurance claims with detailed policy, incident, customer, vehicle, and claim-amount information.  
> **My task in this case study is to perform a fraud detection analysis using SQL.**

---

## Table of Contents
- [Dataset](#dataset)
- [Data Dictionary](#data-dictionary)
- [Business Questions and Solutions](#business-questions-and-solutions)
- [Key Insights and Recommendations](#key-insights-and-recommendations)

---

## Dataset
The analysis uses a single table (`fraud`) imported from an Excel sheet (`Fraud_Detection_decsion tree`) with:

- **Rows:** 1,000  
- **Columns:** 39  
- **Primary Key:** `policy_number`  
- **Target Variable:** `fraud_reported` (Y/N)

---

## Data Dictionary

The `fraud` table contains the following logical groups of columns:

<details>
<summary><strong>Policy Details</strong></summary>

- **policy_number** (PK): Unique policy identifier  
- **policy_bind_date**: Date the policy was bound  
- **policy_state**: State where policy was issued  
- **policy_csl**: Coverage limits (e.g., “100/300”)  
- **policy_deductable**: Deductible amount  
- **umbrella_limit**: Umbrella policy limit  
</details>

<details>
<summary><strong>Incident Details</strong></summary>

- **incident_date**: Date of the incident  
- **incident_type**: Collision, theft, etc.  
- **collision_type**: Single vs. multi-vehicle  
- **incident_severity**: Minor, major damage, etc.  
- **authorities_contacted**: Fire, police, ambulance  
- **incident_state**, **incident_city**, **incident_hour_of_the_day**  
- **property_damage**, **bodily_injuries**, **witnesses**  
- **police_report_available**: Y/N/?  
</details>

<details>
<summary><strong>Customer Demographics</strong></summary>

- **age**: Customer age  
- **months_as_customer**: Tenure in months  
- **insured_sex**: M/F  
- **insured_education_level**: High School, College, etc.  
- **insured_occupation**: Job title  
- **insured_hobbies**: Sports, reading, etc.  
</details>

<details>
<summary><strong>Vehicle Details</strong></summary>

- **auto_make**, **auto_model**, **auto_year**  
</details>

<details>
<summary><strong>Claim Amounts & Target</strong></summary>

- **injury_claim**, **property_claim**, **vehicle_claim**  
- **total_claim_amount**  
- **fraud_reported**: Y/N  
</details>

---

## Business Questions and Solutions

### Question 1: Fraud Rates by Geography  
**Business Problem:** Identify which states have the highest fraud rates to prioritize investigations.  
<details>
<summary>Solution</summary>

```sql
SELECT 
  policy_state,
  ROUND(
    SUM(CASE WHEN fraud_reported = 'Y' THEN 1.0 ELSE 0 END)
    / COUNT(*) * 100
  , 2) AS fraud_rate_percentage
FROM fraud
GROUP BY policy_state
ORDER BY fraud_rate_percentage DESC;
````

</details>
<details>
<summary>Output</summary>

| policy\_state | fraud\_rate\_percentage |
| ------------- | ----------------------- |
| OH            | 25.85                   |
| IN            | 25.48                   |
| IL            | 22.78                   |

</details>

---

### Question 2: Impact of Policy Coverage on Fraud

**Business Problem:** Understand how coverage limits, deductibles, and umbrella limits correlate with fraud incidence.

<details>
<summary>Solution</summary>

```sql
SELECT 
  policy_csl,
  policy_deductable,
  umbrella_limit,
  ROUND(
    SUM(CASE WHEN fraud_reported = 'Y' THEN 1.0 ELSE 0 END)
    / COUNT(*) * 100
  , 2) AS fraud_rate_percentage
FROM fraud
GROUP BY policy_csl, policy_deductable, umbrella_limit
ORDER BY fraud_rate_percentage DESC
LIMIT 3;
```

</details>
<details>
<summary>Output</summary>

| policy\_csl | policy\_deductable | umbrella\_limit | fraud\_rate\_percentage |
| ----------- | ------------------ | --------------- | ----------------------- |
| 100/300     | 1000               | 8000000         | 100.00                  |
| 100/300     | 1000               | 10000000        | 100.00                  |
| 100/300     | 2000               | 8000000         | 100.00                  |

</details>

---

### Question 3: Claim-Amount Patterns (Fraud vs. Legitimate)

**Business Problem:** Compare average claim amounts to spot red-flag thresholds.

<details>
<summary>Solution</summary>

```sql
SELECT 
  fraud_reported,
  ROUND(AVG(injury_claim), 2)       AS avg_injury_claim,
  ROUND(AVG(property_claim), 2)     AS avg_property_claim,
  ROUND(AVG(vehicle_claim), 2)      AS avg_vehicle_claim,
  ROUND(AVG(total_claim_amount), 2) AS avg_total_claim
FROM fraud
GROUP BY fraud_reported;
```

</details>
<details>
<summary>Output</summary>

| fraud\_reported | avg\_injury\_claim | avg\_property\_claim | avg\_vehicle\_claim | avg\_total\_claim |
| --------------- | ------------------ | -------------------- | ------------------- | ----------------- |
| N               | 7,179.23           | 7,018.88             | 36,090.49           | 50,288.61         |
| Y               | 8,208.34           | 8,560.12             | 43,533.64           | 60,302.11         |

</details>

---

### Question 4: Customer Demographics & Fraud Propensity

**Business Problem:** Profile demographic segments to refine risk-scoring.

<details>
<summary>Solution</summary>

```sql
SELECT 
  insured_sex,
  insured_education_level,
  insured_occupation,
  ROUND(
    SUM(CASE WHEN fraud_reported = 'Y' THEN 1.0 ELSE 0 END)
    / COUNT(*) * 100
  , 2) AS fraud_rate_percentage
FROM fraud
GROUP BY insured_sex, insured_education_level, insured_occupation
ORDER BY fraud_rate_percentage DESC
LIMIT 3;
```

</details>
<details>
<summary>Output</summary>

| insured\_sex | insured\_education\_level | insured\_occupation | fraud\_rate\_percentage |
| ------------ | ------------------------- | ------------------- | ----------------------- |
| MALE         | High School               | machine-op-inspct   | 100.00                  |
| FEMALE       | College                   | craft-repair        | 100.00                  |
| MALE         | Associate                 | transport-moving    | 100.00                  |

</details>

---

### Question 5: Vehicle & Incident Characteristics Linked to Fraud

**Business Problem:** Detect vehicle makes/models and incident types most associated with fraud for rule-based alerts.

<details>
<summary>Solution</summary>

```sql
SELECT 
  auto_make,
  auto_model,
  incident_type,
  collision_type,
  incident_severity,
  police_report_available,
  ROUND(
    SUM(CASE WHEN fraud_reported = 'Y' THEN 1.0 ELSE 0 END)
    / COUNT(*) * 100
  , 2) AS fraud_rate_percentage
FROM fraud
GROUP BY auto_make, auto_model, incident_type, collision_type, incident_severity, police_report_available
ORDER BY fraud_rate_percentage DESC
LIMIT 3;
```

</details>
<details>
<summary>Output</summary>

| auto\_make | auto\_model | incident\_type           | collision\_type | incident\_severity | police\_report\_available | fraud\_rate\_percentage |
| ---------- | ----------- | ------------------------ | --------------- | ------------------ | ------------------------- | ----------------------- |
| Accura     | MDX         | Multi-vehicle Collision  | Side Collision  | Major Damage       | ?                         | 100.00                  |
| Accura     | MDX         | Multi-vehicle Collision  | Side Collision  | Major Damage       | NO                        | 100.00                  |
| Accura     | MDX         | Single Vehicle Collision | Rear Collision  | Major Damage       | ?                         | 100.00                  |

</details>

---

## Key Insights and Recommendations

### Key Insights

**Where most fraud happens:** Ohio (26%), Indiana (25%), and Illinois (23%).  
- **Risky policy setup:** Every claim with 100/300 CSL, $1,000 deductible, and $8 M+ umbrella was fraudulent.  
- **Bigger payouts in fraud:** Fraudulent claims average $60 000 vs. $50 000 for legitimate ones (+20%).  
- **High-risk demographic:** High-school-educated machine operators showed 100% fraud.  
- **Crash red flags:** Severe multi-vehicle Accura MDX accidents without a police report were always fraud.

### Business Recommendations

- **Focus on those states:** Increase fraud investigations in OH, IN, and IL.  
- **Tighten big policies:** Require extra proof or add a surcharge for very high-limit policies.  
- **Flag large claims:** Automatically review total claims > $55 000 or injury/property claims > $10 000.  
- **Enhance risk scoring:** Incorporate occupation and education into fraud-risk models.  
- **Set up instant alerts:** Trigger a review for serious crashes lacking a police report.  
```
```

# 📊 GA4 E-Commerce Performance Dashboard (BigQuery SQL & Looker Studio)

This project utilizes public Google Analytics 4 (GA4) e-commerce sample data (BigQuery Public Dataset) to extract key business performance indicators (KPIs) and visualize them using Looker Studio.

The goal is to demonstrate my ability to navigate the complex GA4 schema (specifically nested event parameters), calculate core e-commerce metrics (Revenue, Purchases), and present the findings in a visually clear, user-friendly dashboard.

## 🚀 Project Summary and Outputs

This dashboard monitors the following key metrics for the period of **[November 2020 - January 2021]**:

| Metric Name | Calculated Value |
| :--- | :--- |
| **Total Revenue** | $250.39K |
| **Total Users** | 319,066 |
| **Total Purchases** | 4,466 |
| **Average Order Value (AOV)** | $56.07 |
| **Conversion Rate (CR)** | 1.40% |

### Live Dashboard and Visualization

You can explore the interactive version of the dashboard here:

👉 **[Explore the Live Looker Studio Dashboard](https://lookerstudio.google.com/reporting/9bc6fd44-d8c5-44b0-95c3-e4eeb449dcb2)

The image below shows the main output of the project:

![GA4 E-Commerce Performance Dashboard View]<img width="1351" height="587" alt="dashboard_final png" src="https://github.com/user-attachments/assets/13af12f6-b9cd-438a-810d-33b8a8e8002b" />


---

## 🛠️ Technologies Used

* **Data Source:** `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
* **Data Modeling & Processing:** Google BigQuery (Standard SQL)
* **Data Visualization:** Google Looker Studio (formerly Data Studio)

## 💻 Technical Deep Dive: BigQuery SQL

The most critical technical aspect of this project is extracting meaningful metrics from the complex, nested structure of GA4 event data.

### Key Metric Extraction Points:

1.  **UNNEST Function:** The `event_params` field in GA4 is an `ARRAY`. I utilized the `UNNEST` function to access the critical e-commerce values like `transaction_id` (`value.string_value`) and `value` (`value.double_value`) nested within this array.
2.  **Purchase Metric:** To accurately count `total_purchases`, I combined the `event_name = 'purchase'` condition with a `COUNT(DISTINCT...)` on the extracted `transaction_id` to ensure unique purchases were counted.

The complete SQL query used is provided below:

```sql
SELECT
  parse_date('%Y%m%d', event_date) AS purchase_date,
  COUNT(DISTINCT user_pseudo_id) AS total_users,
  COUNT(DISTINCT CASE WHEN event_name = 'purchase' THEN (SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'transaction_id') END) AS total_purchases,
  SUM(CASE WHEN event_name = 'purchase' THEN (SELECT value.double_value FROM UNNEST(event_params) WHERE key = 'value') END) AS total_revenue
FROM
  `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
WHERE
  _TABLE_SUFFIX BETWEEN '20201101' AND '20210131'
GROUP BY
  purchase_date
ORDER BY
  purchase_date

  

# 📊 GA4 E-Commerce Performance Dashboard (BigQuery SQL & Looker Studio)

This project utilizes public Google Analytics 4 (GA4) e-commerce sample data (BigQuery Public Dataset) to extract key business performance indicators (KPIs) and visualize them using Looker Studio.

The goal is to demonstrate my ability to navigate the complex GA4 schema (specifically nested event parameters), perform deep-dive traffic segmentation, calculate core e-commerce metrics (Revenue, Purchases), and present the findings in a visually clear, user-friendly dashboard.

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

![GA4 E-Commerce Performance Dashboard View]<img width="2542" height="1822" alt="dashboard png" src="https://github.com/user-attachments/assets/97120db1-11ff-4e50-aa5e-518be315cec9" />



---

## 🛠️ Technologies Used

* **Data Source:** `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
* **Data Modeling & Processing:** Google BigQuery (Standard SQL)
* **Data Visualization:** Google Looker Studio (formerly Data Studio)


---
##💡 Key Business Insight (Time Series Analysis)
Analyzing the Time Series Chart (Total Users and Total Purchases Over Time) reveals the following critical observation:

December Peak: December 2020 is a peak for both users and purchases (likely due to holiday sales/promotions).

Insight: While the Total Users saw a sharp increase in December, the corresponding rise in Total Purchases was not proportional to the user growth. This suggests a potential issue with user quality during that period or a bottleneck in the conversion funnel (e.g., site speed, stock issues). This finding would warrant further deep-dive analysis into the December traffic sources and promotional strategies.

### **The Pivot Table Bridge (From 'What' to 'Why'):**

This insight mandated the creation of a second, more granular analysis. I used a **Pivot Table** combined with a new **SQL query** (Grouping by `source` and `medium`) to determine *which specific traffic channels* were responsible for the drop in conversion efficiency. This transition from time-based reporting to channel-based segmentation forms the core of the project's strategic value.


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

# Customer-Segmentation-Revenue-Analysis-FMCG-Domain-
This project explores transactional data from a leading FMCG company to uncover **retailer-like customer behavior patterns** and their influence on revenue.  
Through a combination of **RFM segmentation, revenue profiling, and product/city-level breakdowns**, the goal is to enable better segmentation, pricing, and targeting strategies for both **B2B-like and B2C-like customers**.

---

## 1. Background and Overview

This analysis was conducted on FMCG transaction data over a **14-month period**.  
The client suspected that a segment of their B2C customer base might actually be **retailers purchasing in bulk**.  
These retailer-like customers were driving a significant portion of revenue but were not explicitly labeled.

**Objectives:**
- Detect retailer-like customers using behavioral patterns  
- Quantify their contribution to total orders and revenue  
- Enable the business to create segmented loyalty programs, targeted offers, and better supply chain visibility  

To achieve this, we used a combination of:
- **SQL** for data processing  
- **Custom logic** for segmentation  
- **Power BI** for visualization and storytelling  

---

## 2. Data Structure

The analysis used three core datasets:

### 🧾 Transaction Table
- **Records:** 10,48,575  
- **Key Columns:** `order_number`, `customer_id`, `created_at`, `lineitem_sku`, `revenue`

### 🔗 Order Mapping
- **Records:** 10,48,575  
- **Key Columns:** `order_number`, `customer_id`

### 📦 Product Mapping
- **Records:** 1,774 SKUs  
- **Key Columns:** `lineitem_sku`, `Category`, `Brand`, `Sub Brand`, `Pack Type`

**Data Volume:**
- Over **1 million rows** across datasets  
- Final cleaned dataset had **~39,000 customers** and **1.3K unique SKUs**  
- **Unique Categories:** 35  
- **Top Brands by SKU count:** CLASSMATE, SUNFEAST, AASHIRVAAD  

---

## 3. Executive Summary

- Only **~11%** of customers were retailer-like, but they contributed **~72% of total revenue**.  
- Retailer-like customers showed **higher frequency**, **higher spending**, and **lower recency** — a strong pattern of **regular replenishment**.  
- **BISCUITS, STAPLES, and JUICES** emerged as top categories where retailer behavior was most evident.  
- Major metros like **Bangalore, Kolkata, and New Delhi** contributed significantly to retailer-like revenue.  
- There was a strong **skew in frequency and revenue** — only a handful of customers placed **100+ orders** or spent over **₹1 Lakh**.

- ### 📊 Retailer vs Non-Retailer Revenue Dashboard

![Retailer vs Non-Retailer Dashboard](Dashboards/Retailer%20VS%20NonRetailer%20Dashboard.png)

---

## 4. Deep Dive

### 4.1 EDA & Sanity Checks

- The transaction data contained **10.4 lakh records**, covering orders between **April 2022 and June 2023**.  
- **851,750 records** had null revenue, mostly due to incomplete or failed transactions.  
- City-level data was highly sparse — over **80% of shipping and billing city values** were null.  
- Only **54 unique orders** and **1315 SKUs** were found in the transaction data.  
- **Key missing value checks revealed:**  
  - 1 missing SKU  
  - 548 cancelled orders  
  - 8 refunded orders  
- Frequency of orders was heavily **right-skewed** — over 75% of customers placed only 1 order during the 14-month period.  
- Joined **transaction_table** with **product_mapping** using `lineitem_sku` to retrieve product category and brand.  
- Joined **transaction_table** with **order_mapping** using `order_number` to consolidate customer-level behavior.

---

### 4.2 RFM Segmentation Logic

| Metric    | Formula / Logic |
|-----------|----------------|
| Recency   | Days between last transaction in dataset (June 3, 2023) and customer’s most recent order |
| Frequency | Count of distinct `order_number` per customer |
| Monetary  | Sum of total `revenue` per customer |

- Quartiles were calculated using `PERCENTILE_CONT()` for **Recency** and **Monetary**.  
- Frequency had identical **Q1 = Q2 = Q3 = 1**, so a **custom bucket** was created:
  - Low: 1–7 orders  
  - Medium: 8–21 orders  
  - High: ≥22 orders  

All customers were mapped into **Low**, **Medium**, and **High** for each metric and stored in a consolidated table (`aggregate_table2`).

---

### 4.3 Final RFM Segment Logic

Using a **3x3 RFM matrix**, customers were assigned a final `rfm_segment` based on the following rules:

| Case | Segment |
|------|----------|
| All Low or only 1 Medium | Low |
| 2 Highs | High |
| 2 Mediums + 1 Low | Medium |
| 1 High + 2 Mediums | High |
| Everything else | Medium |

This simplified yet effective logic helped distinguish between **regular**, **occasional**, and **retailer-like** customers.

---

### 4.4 Flagging Retailer-like Behavior

Customers classified as **High RFM segment** were flagged as **Retailer-like**.

**Final Output Table Columns:**
- `customer_id`
- `rfm_segment`
- `retailer_like` → *Yes/No* (Yes = Retailer-like)

---

### 4.5 Contribution Analysis

#### 🧍 Customer Segmentation
- **Retailer-like Customers:** 11%  
- **Regular Customers:** 89%

#### 💰 Revenue Distribution
- **Retailer-like Revenue:** ₹153.11 Cr (72%)  
- **Regular Revenue:** ₹59.69 Cr (28%)

#### 📅 Monthly Revenue Patterns

| Month | Total Revenue | Retailer Share | Regular Share |
|-------|----------------|----------------|----------------|
| Apr 2022 | ₹4.67 Cr | 64% | 36% |
| Dec 2022 – Mar 2023 | — | >75% from Retailers | Restocking or festive demand |

#### 🌆 City-wise Insights (Valid Cities Only)

| City | Retailer Revenue % |
|------|--------------------|
| Bangalore | 83% |
| Kolkata | 79% |
| Chennai | 81% |
| New Delhi | 53% *(regular buyers dominate)* |

#### 🧴 Product Category Insights
- **Retailer-heavy:** Biscuits, Coffee, Bodywash, Handwash  
- **Regular-heavy:** Bath Essentials, Talc, Mask  

---

## 5. Recommendations

### A. For Retailer-like Customers
- **Volume Discounts** to boost order value  
- **Loyalty Tiers (Gold, Silver)** with early stock access  
- **Personalized Alerts** for restocks or new launches  

### B. For Regular Customers
- **Win-back Campaigns** — re-engage low-revenue customers  
- **Targeted Promotions** on personal-use categories  
- **Churn Analysis** for customers inactive for 180+ days  

### C. For Business Teams
- Segment **B2B vs B2C** campaigns  
- Rethink **pricing/bundling** for retailer-heavy categories  
- Clean up **data sources** — especially revenue and location fields  

---

## 🧰 Tools & Technologies
- SQL (PostgreSQL)
- Power BI

---

### Author  
**Debayan Mondal**  
[GitHub Profile](https://github.com/DebayanM1996)

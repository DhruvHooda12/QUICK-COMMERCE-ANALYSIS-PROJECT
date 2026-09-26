# Blinkit Grocery Sales Analysis — SQL & Python

End-to-end sales analysis of **8,523 grocery items across 10 outlets**, run twice: once in **SQL Server** and once in **Python (Pandas, Matplotlib, Seaborn)**. The same business questions are answered in both, and the results match across both.

---

## The Problem

A quick-commerce grocery business needs to know where its revenue actually comes from before it decides which categories to stock, which store formats to open and which cities to expand into. This project turns raw sales data into answers for those decisions:

- What are the headline KPIs: total sales, average sales, item count and average rating?
- Which product categories and fat-content types drive revenue?
- How do outlet size, type, location tier and age affect sales?

---

## Key Insights

| KPI | Value |
|---|---|
| Total Sales | **1.20M** |
| Average Sales | **141** |
| Number of Items | **8,523** |
| Average Rating | **4.0** |

- **Fruits & Vegetables (178K) and Snack Foods (175K)** are the top categories, together about 30% of total sales.
- **Low Fat products make up 65% of revenue.** Regular products make up 35%.
- **Tier 3 cities lead with 39% of sales**, ahead of Tier 2 (33%) and Tier 1 (28%).
- **Medium outlets (42%) outsell High/large outlets (21%)**, so store size doesn't drive revenue.
- **Supermarket Type 1 generates about 66% of all sales**, more than every other outlet type combined.
- **Outlets established in 1998 are the top earners (about 205K).** Every cohort since 2000 plateaus at roughly 130K.

---

## Project Structure

```
blinkit-sales-analysis/
├── blinkit_Grocery_Data.csv          # Raw dataset (8,523 rows × 12 columns)
├── BLINKIT_ANALYSIS.sql              # SQL Server analysis
├── blinkit_analysis_PYTHON.ipynb     # Python analysis with charts
└── README.md
```

---

## Workflow

### 1. Data Cleaning

The raw `Item Fat Content` column had 5 inconsistent labels for 2 real categories. These were standardised in both SQL and Python:

| Raw Value | Cleaned Value |
|---|---|
| `LF`, `low fat`, `Low Fat` | **Low Fat** |
| `reg`, `Regular` | **Regular** |

**SQL:**
```sql
UPDATE Blinkit_Data
SET Item_Fat_Content =
    CASE
        WHEN Item_Fat_Content IN ('LF', 'low fat') THEN 'Low Fat'
        WHEN Item_Fat_Content = 'reg' THEN 'Regular'
        ELSE Item_Fat_Content
    END;
```

**Python:**
```python
df['Item Fat Content'] = df['Item Fat Content'].replace({
    'LF': 'Low Fat', 'low fat': 'Low Fat', 'reg': 'Regular'
})
```

### 2. KPI Calculation

Total Sales, Average Sales, Number of Items and Average Rating.

### 3. Business Analysis

| # | Analysis | SQL | Python |
|---|---|---|---|
| A | Headline KPIs | ✅ | ✅ |
| B | Total sales by fat content | ✅ | ✅ Pie chart |
| C | Total sales by item type | ✅ | ✅ Bar chart |
| D | Fat content by outlet tier | ✅ `PIVOT` | ✅ Grouped bar chart |
| E | Sales by outlet establishment year | ✅ | ✅ Line chart |
| F | Sales % by outlet size | ✅ Window function | ✅ Pie chart |
| G | Sales by outlet location | ✅ | ✅ Horizontal bar chart |
| H | All metrics by outlet type | ✅ | — |

---

## SQL Techniques Used

- `CASE WHEN` for data standardisation
- Aggregations: `SUM`, `AVG`, `COUNT`
- `PIVOT` to reshape fat content by outlet tier
- Window function `SUM(SUM(...)) OVER()` for percentage-of-total calculations
- `CAST` / `DECIMAL` for clean, report-ready output

Example of calculating each outlet size's share of sales:

```sql
SELECT
    Outlet_Size,
    CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales,
    CAST(SUM(Total_Sales) * 100.0 / SUM(SUM(Total_Sales)) OVER() AS DECIMAL(10,2)) AS Sales_Percentage
FROM Blinkit_Data
GROUP BY Outlet_Size
ORDER BY Total_Sales DESC;
```

---

## Dataset

| Column | Description |
|---|---|
| Item Identifier | Unique product ID |
| Item Type | Product category (16 categories) |
| Item Fat Content | Low Fat / Regular |
| Item Weight | Product weight |
| Item Visibility | Share of display area allocated to the product |
| Outlet Identifier | Unique store ID (10 outlets) |
| Outlet Establishment Year | Year the outlet opened (1998–2022) |
| Outlet Size | Small / Medium / High |
| Outlet Location Type | Tier 1 / Tier 2 / Tier 3 |
| Outlet Type | Grocery Store, Supermarket Type 1/2/3 |
| Total Sales | Sales value for the item |
| Rating | Customer rating (1–5) |

---

## Tech Stack

- **SQL Server (T-SQL)** for data cleaning and analysis
- **Python 3.13**
- **Pandas** and **NumPy** for data manipulation
- **Matplotlib** and **Seaborn** for visualisation
- **Jupyter Notebook**

---

## How to Run

### SQL
1. Import `blinkit_Grocery_Data.csv` into SQL Server as a table named `Blinkit_Data`. Use the Import Flat File wizard in SSMS, and replace spaces in column names with underscores.
2. Open `BLINKIT_ANALYSIS.sql` in SSMS and run the queries in order.

### Python
```bash
git clone https://github.com/<your-username>/blinkit-sales-analysis.git
cd blinkit-sales-analysis
pip install pandas numpy matplotlib seaborn jupyter
jupyter notebook blinkit_analysis_PYTHON.ipynb
```

---

## Related Project

📊 **[Blinkit Sales Dashboard — Power BI](#)**: the same dataset turned into an interactive dashboard.

---

## Author

**Dhruv Hooda** —(dhruvh.work@gmail.com)


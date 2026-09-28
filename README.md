# 🛒 Retail Sales Analysis — SQL Project

## 📌 Project Overview

**Retail Sales Analysis** is an end-to-end SQL data analysis project focused on exploring retail transaction data and extracting meaningful business insights using SQL.

The project covers **database creation, data cleaning, exploratory data analysis (EDA), customer analysis, sales performance analysis, and business-focused SQL queries**.

The goal is to demonstrate practical SQL skills that are commonly required for **Data Analyst and Business Analyst roles**.

---

## 🎯 Project Objectives

- Create and manage a retail sales database.
- Clean and validate raw sales data.
- Perform exploratory data analysis using SQL.
- Analyze sales performance across product categories.
- Identify customer purchasing patterns.
- Analyze sales trends by month and year.
- Identify high-value transactions and top customers.
- Analyze order distribution across different time shifts.
- Answer real-world business questions using SQL.

---

## 🛠️ Technologies Used

- **SQL**
- **PostgreSQL**
- **SQL Window Functions**
- **CTEs (Common Table Expressions)**
- **Aggregate Functions**
- **GROUP BY & HAVING**
- **Subqueries**
- **CASE Statements**
- **Date & Time Functions**

---

## 🗄️ Database Structure

### Database

```text
p1_retail_db

```

### Main Table

```text
retail_sales

```

### Table Columns

| Column            | Description                   |
| ----------------- | ----------------------------- |
| `transactions_id` | Unique transaction identifier |
| `sale_date`       | Date of the transaction       |
| `sale_time`       | Time of the transaction       |
| `customer_id`     | Unique customer identifier    |
| `gender`          | Customer gender               |
| `age`             | Customer age                  |
| `category`        | Product category              |
| `quantity`        | Quantity purchased            |
| `price_per_unit`  | Price per unit                |
| `cogs`            | Cost of goods sold            |
| `total_sale`      | Total transaction amount      |

---

## 📂 Project Workflow

The project follows the following data analysis workflow:

```text
Raw Sales Data
      ↓
Database & Table Creation
      ↓
Data Cleaning
      ↓
Data Exploration
      ↓
SQL Analysis
      ↓
Business Insights
      ↓
Final Findings

```

---

# 🔍 1. Database Setup

The project begins by creating the retail sales database and the `retail_sales` table.

```sql
CREATE DATABASE p1_retail_db;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);

```

---

# 🧹 2. Data Cleaning & Exploration

The dataset was checked for:

- Total number of records
- Unique customers
- Unique product categories
- Missing or NULL values
- Data completeness

### Check Total Records

```sql
SELECT COUNT(*)
FROM retail_sales;

```

### Count Unique Customers

```sql
SELECT COUNT(DISTINCT customer_id)
FROM retail_sales;

```

### Identify Product Categories

```sql
SELECT DISTINCT category
FROM retail_sales;

```

### Check for Missing Values

```sql
SELECT *
FROM retail_sales
WHERE sale_date IS NULL
   OR sale_time IS NULL
   OR customer_id IS NULL
   OR gender IS NULL
   OR age IS NULL
   OR category IS NULL
   OR quantity IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL;

```

Records containing missing values were removed before performing the analysis.

---

# 📊 3. Business Analysis

The following business questions were answered using SQL.

### 1. Sales on a Specific Date

Retrieve all transactions made on **November 5, 2022**.

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';

```

---

### 2. Clothing Sales in November 2022

Find Clothing transactions where the quantity sold was **4 or more** during November 2022.

```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
  AND quantity >= 4;

```

---

### 3. Total Sales by Category

Calculate total sales and number of orders for each product category.

```sql
SELECT
    category,
    SUM(total_sale) AS total_sales,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;

```

---

### 4. Average Customer Age — Beauty Category

Calculate the average age of customers who purchased products from the Beauty category.

```sql
SELECT
    ROUND(AVG(age), 2) AS average_age
FROM retail_sales
WHERE category = 'Beauty';

```

---

### 5. High-Value Transactions

Identify transactions where the total sale amount exceeded 1000.

```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;

```

---

### 6. Transactions by Gender and Category

Calculate the number of transactions for each gender across product categories.

```sql
SELECT
    category,
    gender,
    COUNT(*) AS total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;

```

---

### 7. Best-Selling Month by Year

Calculate the average sale for each month and identify the month with the highest average sale for each year.

This analysis uses:

- `EXTRACT()`
- `GROUP BY`
- `AVG()`
- `RANK()`
- Window Functions

```sql
SELECT
    year,
    month,
    avg_sale
FROM
(
    SELECT
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY 1, 2
) AS monthly_sales
WHERE rank = 1;

```

---

### 8. Top 5 Customers by Total Spending

Identify the five customers who generated the highest total sales.

```sql
SELECT
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;

```

---

### 9. Unique Customers by Category

Calculate the number of unique customers purchasing from each product category.

```sql
SELECT
    category,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;

```

---

### 10. Orders by Time Shift

Classify transactions into:

- **Morning:** Before 12 PM
- **Afternoon:** 12 PM–5 PM
- **Evening:** After 5 PM

```sql
WITH hourly_sales AS
(
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12
                THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17
                THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)

SELECT
    shift,
    COUNT(*) AS total_orders
FROM hourly_sales
GROUP BY shift
ORDER BY total_orders DESC;

```

---

# 📈 Key Analysis Areas

The project provides insights into several areas of retail performance:

### 👥 Customer Analysis

- Unique customer counts
- Customer demographics
- Top-spending customers
- Customer distribution by category

### 🛍️ Product Category Analysis

- Sales by category
- Transaction volume by category
- Customer distribution across categories

### 📅 Sales Trend Analysis

- Monthly sales performance
- Yearly sales patterns
- Best-performing months

### 💰 Revenue Analysis

- Total sales
- High-value transactions
- Average transaction values

### 🕒 Time-Based Analysis

- Morning, afternoon, and evening order volumes
- Identification of high-activity sales periods

---

# 💡 Key Findings

The analysis can be used to identify:

- Product categories generating higher sales.
- Customer segments contributing significantly to revenue.
- High-value transactions and purchasing patterns.
- Monthly variations in sales performance.
- Peak periods based on transaction timing.
- Customers with the highest total spending.

> **Note:** Specific numerical findings should be added here after running the queries against the final dataset.

---

# 📁 Project Structure

Recommended GitHub repository structure:

```text
Retail-Sales-Analysis-SQL/
│
├── README.md
│
├── Retail_Sales_Analysis.sql
│
├── dataset/
│   └── retail_sales.csv
│
└── screenshots/
    └── sql_results.png

```

---

# ▶️ How to Run the Project

### Step 1 — Clone the Repository

```bash
git clone <your-github-repository-url>

```

### Step 2 — Open PostgreSQL

Open the project in **PostgreSQL / pgAdmin / your preferred SQL environment**.

### Step 3 — Create the Database

Run the database and table creation queries.

### Step 4 — Import the Dataset

Load the retail sales dataset into the `retail_sales` table.

### Step 5 — Run the Analysis Queries

Execute the SQL queries provided in:

```text
Retail_Sales_Analysis.sql

```

### Step 6 — Explore the Results

Modify the queries or create additional queries to discover further business insights.

---

# 📚 SQL Skills Demonstrated

This project demonstrates practical knowledge of:

```text
✓ SELECT
✓ WHERE
✓ DISTINCT
✓ GROUP BY
✓ ORDER BY
✓ LIMIT
✓ Aggregate Functions
✓ COUNT()
✓ SUM()
✓ AVG()
✓ ROUND()
✓ CASE
✓ CTEs
✓ Subqueries
✓ Window Functions
✓ RANK()
✓ Date Functions
✓ Time Functions
✓ Data Cleaning
✓ Exploratory Data Analysis

```

---

# 🎓 Project Purpose

This project was created as part of my **Data Analytics portfolio** to demonstrate practical SQL skills and the ability to transform raw retail transaction data into meaningful business insights.

It demonstrates how SQL can be used to perform **data cleaning, exploratory analysis, customer analysis, sales analysis, and business problem solving**.

---

# 👨‍💻 Author

**Solomon Isaac**

Aspiring Data Analyst | SQL | Excel | Power BI | Python

---

## ⭐ If you found this project useful

Feel free to explore the SQL queries, modify them, and extend the project with additional business questions and analysis.
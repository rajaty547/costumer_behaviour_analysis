# 📊 Customer Trend Analysis

An end-to-end **Customer Shopping Behavior Analysis** project using **Python, SQL, SQLite, and Power BI** to transform raw customer transaction data into meaningful business insights and an interactive dashboard.

The project covers the complete analytics workflow — from data cleaning and feature engineering to SQL-based business analysis and Power BI visualization.

---

## 📌 Project Overview

Understanding customer purchasing behavior is important for identifying revenue patterns, customer segments, product performance, subscription trends, and purchasing preferences.

In this project, a customer shopping dataset containing **3,900 customer records** was analyzed to answer practical business questions such as:

* How does revenue differ between male and female customers?
* Which customers use discounts while still spending above average?
* Which products have the highest average review ratings?
* Does shipping type affect average purchase amount?
* Do subscribed customers spend more than non-subscribers?
* Which products have the highest discount usage?
* How can customers be segmented based on previous purchases?
* What are the most purchased products within each category?
* Are repeat buyers more likely to subscribe?
* Which age groups contribute the most revenue?

The analysis was then converted into an interactive **Power BI dashboard** for visual exploration.

---

## 🎯 Objectives

The main objectives of this project are to:

1. Clean and preprocess customer shopping data.
2. Handle missing values and inconsistent column names.
3. Create useful analytical features such as age groups and purchase frequency in days.
4. Store the processed data in a SQLite database.
5. Perform business-oriented analysis using SQL.
6. Identify customer purchasing and revenue patterns.
7. Build an interactive Power BI dashboard.
8. Present the findings in a clear and business-friendly format.

---

## 🛠️ Tech Stack

| Technology                | Purpose                                   |
| ------------------------- | ----------------------------------------- |
| 🐍 Python                 | Data cleaning, preprocessing and analysis |
| 🐼 Pandas                 | Data manipulation and transformation      |
| 🗄️ SQLite                | Structured data storage and SQL analysis  |
| 🔎 SQL                    | Business queries and customer analysis    |
| 📊 Power BI               | Interactive dashboard and visualization   |
| 📓 Jupyter / Google Colab | Data analysis environment                 |
| 📄 CSV                    | Cleaned dataset storage                   |

---

## 📂 Project Structure

```text
customer-trend-analysis/
│
├── 📓 customer trend analysis.ipynb
│       └── Complete Python + SQL analysis
│
├── 📊 customer_trends_cleaned.csv
│       └── Cleaned and feature-engineered dataset
│
├── 📈 customer_behavior_dashboard.pbix
│       └── Interactive Power BI dashboard
│
└── 📄 README.md
        └── Project documentation
```

---

# 🔄 Project Workflow

```text
Raw Customer Data
        ↓
Data Loading
        ↓
Data Exploration
        ↓
Data Cleaning
        ↓
Feature Engineering
        ↓
SQLite Database
        ↓
SQL Business Analysis
        ↓
Cleaned CSV
        ↓
Power BI Dashboard
        ↓
Business Insights
```

---

# 🧹 1. Data Cleaning & Preprocessing

The project begins by loading the customer shopping dataset using Pandas.

### Initial exploration

The dataset was inspected using:

```python
df.head()
df.info()
df.describe(include="all")
df.isnull().sum()
```

This helped understand:

* Dataset structure
* Data types
* Missing values
* Numerical statistics
* Categorical variables

### Handling missing values

Missing values in the `Review Rating` column were filled using the **median review rating of the corresponding product category**.

```python
df['Review Rating'] = df.groupby('Category')['Review Rating'].transform(
    lambda x: x.fillna(x.median())
)
```

---

# 🔧 2. Feature Engineering

Several transformations were performed to make the data more useful for analysis.

### Standardizing column names

Column names were converted to lowercase and spaces were replaced with underscores.

```python
df.columns = df.columns.str.lower()
df.columns = df.columns.str.replace(' ', '_')
```

The purchase amount column was also renamed:

```text
purchase_amount_(usd)
        ↓
purchase_amount
```

---

## 👥 Age Group

Customers were divided into four age groups using quartile-based segmentation:

* Young Adult
* Adult
* Middle-aged
* Senior

```python
labels = ['Young Adult', 'Adult', 'Middle-aged', 'Senior']

df['age_group'] = pd.qcut(
    df['age'],
    q=4,
    labels=labels
)
```

This feature makes it easier to analyze revenue and purchasing behavior across customer age groups.

---

## ⏱️ Purchase Frequency

The original purchase-frequency categories were converted into approximate numbers of days.

| Purchase Frequency | Days |
| ------------------ | ---: |
| Weekly             |    7 |
| Fortnightly        |   14 |
| Bi-Weekly          |   14 |
| Monthly            |   30 |
| Quarterly          |   90 |
| Every 3 Months     |   90 |
| Annually           |  365 |

This created the new:

```text
purchase_frequency_days
```

column.

---

## 🧹 Redundant Column Removal

The project checked whether:

```text
discount_applied
```

and

```text
promo_code_used
```

contained equivalent information.

After confirming the relationship in the dataset, `promo_code_used` was removed from the analysis.

---

# 🗄️ 3. SQLite Database

After preprocessing, the data was loaded into a SQLite database.

```python
import sqlite3

conn = sqlite3.connect("customer_trends.db")
```

The cleaned DataFrame was then stored as:

```text
customer_data
```

This allowed SQL queries to be used for business analysis.

---

# 🔎 4. SQL Business Analysis

The project contains **10 business-focused SQL questions**.

### Q1 — Revenue by Gender

Calculates total revenue generated by male and female customers.

```sql
SELECT gender,
       SUM(purchase_amount) AS revenue
FROM customer_data
GROUP BY gender;
```

---

### Q2 — Discounted Purchases Above Average

Identifies customers who:

* Used a discount
* Still spent more than the average purchase amount

```sql
SELECT customer_id,
       purchase_amount
FROM customer_data
WHERE discount_applied = 'Yes'
AND purchase_amount >
    (SELECT AVG(purchase_amount)
     FROM customer_data);
```

---

### Q3 — Top Products by Average Rating

Finds the five products with the highest average review rating.

```sql
SELECT item_purchased,
       ROUND(AVG(review_rating), 2) AS "Average Product Rating"
FROM customer_data
GROUP BY item_purchased
ORDER BY AVG(review_rating) DESC
LIMIT 5;
```

---

### Q4 — Standard vs Express Shipping

Compares the average purchase amount for Standard and Express shipping.

---

### Q5 — Subscribers vs Non-Subscribers

Compares:

* Number of customers
* Average spending
* Total revenue

between subscribers and non-subscribers.

---

### Q6 — Products with Highest Discount Rate

Identifies the five products with the highest percentage of purchases where discounts were applied.

---

### Q7 — Customer Segmentation

Customers are segmented based on their previous purchases:

| Previous Purchases | Segment   |
| ------------------ | --------- |
| 1                  | New       |
| 2–10               | Returning |
| >10                | Loyal     |

This helps analyze customer retention and repeat purchasing behavior.

---

### Q8 — Top Products Within Each Category

Uses SQL window functions to identify the top three most purchased products within each product category.

---

### Q9 — Repeat Buyers & Subscription

Analyzes subscription status among customers with more than five previous purchases.

---

### Q10 — Revenue by Age Group

Calculates total revenue contributed by each age group.

This analysis is also useful for demographic-based customer segmentation.

---

# 📊 5. Power BI Dashboard

The cleaned customer data was visualized using **Microsoft Power BI**.

The dashboard provides an interactive view of important customer metrics and allows users to explore the data through filters and visualizations.

### Dashboard components include:

* 👥 Customer count
* 💰 Average purchase amount
* ⭐ Average review rating
* 📊 Customer/category visualizations
* 🍩 Category distribution
* 👤 Subscription analysis
* 🚻 Gender filtering
* 🚚 Shipping type filtering
* 📦 Product/category analysis
* 📈 Customer purchasing trends

The dashboard includes interactive slicers for areas such as:

* Subscription Status
* Gender
* Category
* Shipping Type

This allows users to dynamically explore different customer segments.

---

# 📁 Dataset

The final cleaned dataset contains:

**3,900 rows × 19 columns**

### Main fields

```text
customer_id
age
gender
item_purchased
category
purchase_amount
location
size
color
season
review_rating
subscription_status
shipping_type
discount_applied
previous_purchases
payment_method
frequency_of_purchases
age_group
purchase_frequency_days
```

---

# 📈 Key Analytical Areas

The project focuses on several important dimensions of customer behavior:

### 👥 Customer Demographics

* Age
* Age group
* Gender
* Location

### 🛍️ Purchasing Behavior

* Products purchased
* Categories
* Purchase amount
* Previous purchases
* Purchase frequency

### 💳 Customer Engagement

* Subscription status
* Payment method
* Discount usage

### 🚚 Fulfillment

* Shipping type

### ⭐ Customer Feedback

* Review ratings

### 🌦️ Seasonal Analysis

* Seasonal purchasing patterns

---

# 💡 Business Questions Answered

The project demonstrates how data analytics can be used to investigate questions around:

**Revenue**

> Who contributes the most revenue?

**Customer segmentation**

> How many customers are new, returning, or loyal?

**Subscriptions**

> How does purchasing behavior differ between subscribers and non-subscribers?

**Discount strategy**

> Which products have the highest discount usage?

**Product performance**

> Which products receive the highest ratings and generate the most purchases?

**Demographics**

> How does revenue vary across different age groups and genders?

**Shipping**

> How does average spending compare across shipping methods?

---

# 🚀 How to Run the Project

## 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/customer-trend-analysis.git
```

```bash
cd customer-trend-analysis
```

---

## 2. Install Python dependencies

```bash
pip install pandas jupyter
```

SQLite is included with Python through the `sqlite3` module.

---

## 3. Open the notebook

Run:

```bash
jupyter notebook
```

Then open:

```text
customer trend analysis.ipynb
```

Alternatively, the notebook can be run using **Google Colab**.

---

## 4. Run the analysis

Execute the notebook cells sequentially.

The notebook will:

1. Load the dataset.
2. Explore the data.
3. Clean missing values.
4. Standardize columns.
5. Create analytical features.
6. Create the SQLite database.
7. Execute SQL queries.
8. Export the cleaned dataset.

---

# 📊 Power BI Dashboard

To explore the dashboard:

1. Install **Microsoft Power BI Desktop**.
2. Open:

```text
customer_behavior_dashboard.pbix
```

3. Interact with the available filters and visualizations.

> Note: Power BI `.pbix` files are intended to be opened using Power BI Desktop.

---

# 📚 Skills Demonstrated

This project demonstrates practical experience with:

* Data Cleaning
* Data Preprocessing
* Exploratory Data Analysis
* Feature Engineering
* Pandas
* Python
* SQL
* SQLite
* Customer Segmentation
* Business Analytics
* Data Visualization
* Power BI
* Dashboard Development
* Analytical Problem Solving

---

# 🎓 Project Learning Outcomes

Through this project, the following end-to-end analytics workflow was practiced:

```text
Raw Data
   ↓
Data Understanding
   ↓
Data Cleaning
   ↓
Feature Engineering
   ↓
Database Creation
   ↓
SQL Analysis
   ↓
Data Export
   ↓
Power BI Visualization
   ↓
Business Insights
```

The project demonstrates how raw customer transaction data can be transformed into structured information and interactive business intelligence.

---

# 🔮 Future Improvements

Possible extensions to this project include:

* Add more advanced customer segmentation using RFM analysis.
* Build customer lifetime value metrics.
* Add cohort analysis.
* Analyze customer retention over time.
* Create additional Power BI drill-through pages.
* Add geographic visualizations.
* Develop predictive models for customer subscription or repeat purchases.
* Automate the data pipeline from raw data to dashboard.
* Add automated KPI reporting.

---

# 👨‍💻 Author

**Rajat Yadav**

B.Tech — Computer Science & Engineering

Interested in:

* Data Analytics
* Data Science
* Machine Learning
* Full-Stack Development
* Business Intelligence

---

## ⭐ If you found this project useful

Feel free to ⭐ the repository and explore the analysis, SQL queries, cleaned dataset, and Power BI dashboard.

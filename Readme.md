# SQL Data Analytics Project

## 📊 Overview

This repository showcases a comprehensive SQL data analytics project focused on building a modern data warehouse using SQL Server. 
The project demonstrates end-to-end data pipeline development, including ETL processes, data modeling, and advanced analytics techniques.

## 🎯 Project Objectives

- Build a robust data warehouse architecture using SQL Server
- Implement efficient ETL (Extract, Transform, Load) processes
- Create optimized data models for analytical workloads
- Demonstrate advanced SQL analytics and reporting capabilities
- Showcase best practices in data warehousing and business intelligence

## 🛠 Technologies Used

- **Database**: Microsoft SQL Server
- **Languages**: SQL (T-SQL)
- **Tools**: SQL Server Management Studio (SSMS)
- **Concepts**: Data Warehousing, ETL, Data Modeling, Business Intelligence

## 📁 Project Structure

```
sql-data-analytics-project/
├── datasets/
│   └── csv-files/          # Raw CSV data files
│       ├── gold.dim_customers.csv
│       ├── gold.dim_products.csv
│       └── gold.fact_sales.csv
├── 00_init_database.sql    # Database and schema creation
├── 01_database_exploration.sql
├── 02_dimensions_exploration.sql
├── 03_date_range_exploration.sql
├── 04_measures_exploration.sql
├── 05_magnitude_analysis.sql
├── 06_ranking_analysis.sql
├── 07_change_over_time_analysis.sql
├── 08_cumulative_analysis.sql
├── 09_performance_analysis.sql
├── 10_data_segmentation.sql
├── 11_part_to_whole_analysis.sql
├── 12_report_customers.sql
├── 13_report_products.sql
└── README.md
```

## 🚀 Getting Started

### Prerequisites

- Microsoft SQL Server (2016 or later)
- SQL Server Management Studio (SSMS)
- Basic understanding of SQL and data warehousing concepts

### Installation & Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/Amanpathak8/sql-data-analytics-project.git
   cd sql-data-analytics-project
   ```

2. **Database Setup**
   - Connect to your SQL Server instance
   - Run the database creation scripts in the `scripts/ddl/` folder
   - Execute table creation scripts in the correct order

3. **Execute Analysis Scripts**
   - Run scripts in numerical order (01_ through 13_)
   - Each script builds upon previous analysis
   - View results and insights from each analysis type

4. **Generate Business Intelligence**
   - Execute customer report view: `12_report_customers.sql`
   - Execute product report view: `13_report_products.sql`
   - Query the views for comprehensive business insights

## 📈 Key Features

### Data Warehouse Architecture
- **Database**: DataWarehouseAnalytics with gold schema
- **Dimensional Modeling**: Star schema implementation
- **Fact Table**: `gold.fact_sales` - Transaction-level sales data
- **Dimension Tables**: 
  - `gold.dim_customers` - Customer demographics and attributes
  - `gold.dim_products` - Product catalog with categories and subcategories

### ETL Processes
- **Database Creation**: `00_init_database.sql` - Complete database setup with BULK INSERT operations
- **Data Loading**: Automated CSV import using SQL Server BULK INSERT
- **Data Quality**: Schema validation and data type enforcement

### Analytics & Reporting
- **Exploratory Analysis**: Database structure and data profiling (`01_database_exploration.sql`)
- **Dimension Analysis**: Unique value exploration (`02_dimensions_exploration.sql`)
- **Temporal Analysis**: Date range and time-based insights (`03_date_range_exploration.sql`)
- **Business Metrics**: Key performance indicators (`04_measures_exploration.sql`)
- **Customer Analytics**: Segmentation and behavior analysis (`10_data_segmentation.sql`, `12_report_customers.sql`)
- **Product Analytics**: Performance tracking and categorization (`13_report_products.sql`)
- **Advanced Analytics**: Ranking, cumulative, and performance analysis (`06-09_*.sql`)

## 🔍 Key Analytics Examples

### Sales Performance Analysis
**File**: `07_change_over_time_analysis.sql`

```sql
-- Analyze sales performance over time
SELECT
    YEAR(order_date) AS order_year,
    MONTH(order_date) AS order_month,
    SUM(sales_amount) AS total_sales,
    COUNT(DISTINCT customer_key) AS total_customers,
    SUM(quantity) AS total_quantity
FROM gold.fact_sales
WHERE order_date IS NOT NULL
GROUP BY YEAR(order_date), MONTH(order_date)
ORDER BY YEAR(order_date), MONTH(order_date);
```

### Customer Segmentation
**File**: `10_data_segmentation.sql`

```sql
-- Segment customers based on spending behavior and lifespan
WITH customer_spending AS (
    SELECT
        c.customer_key,
        SUM(f.sales_amount) AS total_spending,
        DATEDIFF(month, MIN(order_date), MAX(order_date)) AS lifespan
    FROM gold.fact_sales f
    LEFT JOIN gold.dim_customers c
        ON f.customer_key = c.customer_key
    GROUP BY c.customer_key
)
SELECT 
    CASE 
        WHEN lifespan >= 12 AND total_spending > 5000 THEN 'VIP'
        WHEN lifespan >= 12 AND total_spending <= 5000 THEN 'Regular'
        ELSE 'New'
    END AS customer_segment,
    COUNT(customer_key) AS total_customers
FROM customer_spending
GROUP BY customer_segment;
```

### Product Performance Ranking
**File**: `06_ranking_analysis.sql`

```sql
-- Top 5 products by revenue with ranking
SELECT *
FROM (
    SELECT
        p.product_name,
        SUM(f.sales_amount) AS total_revenue,
        RANK() OVER (ORDER BY SUM(f.sales_amount) DESC) AS rank_products
    FROM gold.fact_sales f
    LEFT JOIN gold.dim_products p
        ON p.product_key = f.product_key
    GROUP BY p.product_name
) AS ranked_products
WHERE rank_products <= 5;
```

## 📊 Analysis Categories

### 1. **Exploratory Analysis**
- **Database Exploration** (`01_database_exploration.sql`): Schema and table structure analysis
- **Dimension Exploration** (`02_dimensions_exploration.sql`): Unique values and data distribution
- **Date Range Analysis** (`03_date_range_exploration.sql`): Temporal data boundaries

### 2. **Business Metrics**
- **Key Measures** (`04_measures_exploration.sql`): Total sales, quantities, averages, and business KPIs
- **Magnitude Analysis** (`05_magnitude_analysis.sql`): Data quantification across dimensions

### 3. **Performance Analytics**
- **Ranking Analysis** (`06_ranking_analysis.sql`): Top performers and product rankings
- **Temporal Trends** (`07_change_over_time_analysis.sql`): Time-series analysis and seasonality
- **Cumulative Analysis** (`08_cumulative_analysis.sql`): Running totals and moving averages
- **Performance Comparison** (`09_performance_analysis.sql`): Year-over-year analysis with LAG functions

### 4. **Advanced Analytics**
- **Customer Segmentation** (`10_data_segmentation.sql`): Behavioral grouping and spend analysis
- **Part-to-Whole Analysis** (`11_part_to_whole_analysis.sql`): Category contribution percentages

### 5. **Business Intelligence Views**
- **Customer Report** (`12_report_customers.sql`): Comprehensive customer analytics view
- **Product Report** (`13_report_products.sql`): Product performance and lifecycle analysis

## 🏗 Data Model

### Database: DataWarehouseAnalytics
**Schema**: `gold` (production-ready, cleaned data)

### Fact Table
- **`gold.fact_sales`**: Core transaction data
  - `order_number`, `product_key`, `customer_key`
  - `order_date`, `shipping_date`, `due_date`
  - `sales_amount`, `quantity`, `price`

### Dimension Tables
- **`gold.dim_customers`**: Customer master data
  - Demographics: `first_name`, `last_name`, `gender`, `birthdate`
  - Location: `country`
  - Attributes: `marital_status`, `customer_number`

- **`gold.dim_products`**: Product catalog
  - Identity: `product_number`, `product_name`
  - Hierarchy: `category`, `subcategory`
  - Attributes: `cost`, `product_line`, `maintenance`

## 🔧 Performance Optimization

- **Indexing Strategy**: Optimized indexes for analytical workloads
- **Partitioning**: Table partitioning for large datasets
- **Query Optimization**: Efficient query patterns and best practices
- **Materialized Views**: Pre-computed aggregations for faster reporting

## 📝 Key Learnings

- Data warehouse design principles and best practices
- ETL process automation and error handling
- Advanced SQL techniques for complex analytics
- Performance tuning for analytical workloads
- Business intelligence and reporting methodologies



## 📧 Contact

**Aman Pathak**
- GitHub: [@Amanpathak8](https://github.com/Amanpathak8)
 Email: amanpathak8802@gmail.com

## 🙏 Acknowledgments

- SQL Server documentation and community resources
- Data warehousing best practices from industry experts
- Open-source SQL analytics projects for inspiration

# What are your risk areas? Identify and describe them.

    During the work on the project, I have identified the following risk areas that directly can affect the completeness, uniqueness, and consistency of the data:

        - Null values. Certain columns of the tables contain either empty values or only a few populated values. Deleting null data requires extra caution in the absence of information about columns that will not be needed and will not be updated in the future.
        - Duplicate values. They appear in multiple tables, lead to faulty conclusions, and affect the identification of the primary key.
        - Inconsistent values. Several tables exhibit duplicate columns with similar types of data, making it hard to know which table contains the data more suited for analysis. Also, data in the same named columns do not always correspond in different tables.
        - Identifying Primary and Foreign Keys. The absence of suitable primary key columns posed risks when joining and integrating data, as well as finding relationships in different tables of the database.
        - Not complete data. For instance, the format of price numbers that are overinflated, wrong or missing terms in columns with country and city names. These types of data are critical for an e-commerce database.

QA Process:
Describe your QA process and include the SQL queries used to execute it.

# Null values. 
- Examples of queries to check if there are columns with null values or empty columns, IS NULL or IS NOT NULL statements were used.

---
SELECT currency_code
FROM public.all_sessions
WHERE currency_code IS NULL;
---
SELECT *
FROM public.analytics
WHERE units_sold IS NOT NULL;
---

- Example of the query to determine the number of zero values of product price. Checking existing rows with zero values and their comparison with the total amount of rows in some cases can be revealing for analysis.  

---
SELECT COUNT (*) AS product_price_zero,
   (SELECT
		COUNT(*) AS Number_Of_Rows
		FROM all_sessions)
FROM public.all_sessions
WHERE product_price = 0;
---

# Duplicate values.
- Example of query to verify data uniqueness. As a result, duplicate data were determined.

---
SELECT *
FROM public.all_sessions
WHERE fullvisitor_id IN (
    SELECT fullvisitor_id
	FROM public.all_sessions
    GROUP BY fullvisitor_id
    HAVING COUNT(*) > 1)
ORDER BY visit_id;
---

- Example of query to delete duplicate data if it does not contradict business requirements.

---
DELETE FROM public.all_sessions
WHERE fullvisitor_id IN (
    SELECT fullvisitor_id
    FROM public.all_sessions
    GROUP BY fullvisitor_id
    HAVING COUNT(*) > 1);
---


# Inconsistent values.
- Example of query to change a name of a column to match the name of a column in another table. Also can be done with pgAdmin tool (right-click table_name-properties-columns).

---
ALTER TABLE public.products
RENAME COLUMN " SKU" TO product_SKU;
---

- Example of query to convert values to appropriate data type.  

---
ALTER TABLE public.analytics
ALTER COLUMN visit_start_time TYPE time
USING TIME '00:00:00' + visit_start_time * INTERVAL '1 second';
---

# Identifying Primary and Foreign Keys.
- Examples of queries to verify uniqueness and not null values (mandatory conditions for primary key).

---
SELECT DISTINCT product_sku
FROM public.products;
---
SELECT *
FROM public.products
WHERE product_sku IS NULL;
---

- Example of query to identify the existence of primary key and foreign key between two tables. The result tells about the absence of them.

---
SELECT a.visit_id, a.fullvisitor_id
FROM public.analytics a
LEFT JOIN public.all_sessions s ON a.visit_id = s.visit_id
WHERE s.visit_id IS NULL;
---

# Not complete data.
-Example of query to check that countries and cities are consistent with each other. The result shows that it seems that data require further investigation.

---
SELECT 	country, city 
FROM public.all_sessions
GROUP BY country, city
ORDER BY country;
---

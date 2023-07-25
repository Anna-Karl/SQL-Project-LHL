# What issues will you address by cleaning the data?

 Before creating the database, the data was analyzed in the source tables to set the correct data type and same-style column names (snake_case).
 First step: Check for the existing null values, their replacing and deleting.
 Second step: Check and remove duplicate entries
 Third step: Check the data types of columns and change them to relevant ones.
 Forth step: Converting monetary columns (price, revenue) to appropriate format.
 Fifth step: Editing string values (fixing typos)
 Sixth step: Link tables by defining primary and foreign keys.


# Queries:
Below, provide the SQL queries you used to clean your data.

- Checking columns with null values (one of the many executed queries for example):
---
SELECT *
FROM public.all_sessions
WHERE currency_code IS NULL;
---

- Replacing null values for suitable values:
---
UPDATE public.all_sessions
SET currency_code = 'USD' WHERE currency_code IS NULL;
---

- Replacing null values in numeric columns with 0:
---
UPDATE public.all_sessions 
SET time_on_site = COALESCE(time_on_site, '0');
---
UPDATE public.analytics
SET time_on_site = COALESCE(time_on_site, '0');
---

- Deleting rows that contain null values in most columns:
---
DELETE FROM public.products
WHERE name IS NULL;
---

- Checking duplicate values:
---
SELECT* FROM public.all_sessions
WHERE visit_id IN (
    SELECT visit_id
    FROM public.all_sessions
	GROUP BY visit_id
    HAVING COUNT(*) > 1
)
ORDER BY visit_id;
---
(In this particular case, the rows have the same data in several columns, but as well have different information about time, product price and name, et. c. So, deleting duplicates, in this case, can lead to false conclusions).

- Returning data types of all columns table (one of the executed queries for example):
---
SELECT *
FROM INFORMATION_SCHEMA.COLUMNS
WHERE table_name = 'public.all_sessions';
---

- Converting values to time data type:  
---
ALTER TABLE public.analytics
ALTER COLUMN visit_start_time TYPE time
USING TIME '00:00:00' + visit_start_time * INTERVAL '1 second';
---

- Changing data types from varchar to numeric:
---
ALTER TABLE public.all_sessions
ALTER COLUMN product_price TYPE numeric USING (product_price::numeric(20,2)),
ALTER COLUMN product_revenue TYPE numeric USING (product_revenue::numeric(20,2)),
ALTER COLUMN item_revenue TYPE numeric USING (item_revenue::numeric(20,2)),
ALTER COLUMN total_transaction_revenue TYPE numeric USING (total_transaction_revenue::numeric(20,2)),
ALTER COLUMN transaction_revenue TYPE numeric USING (transaction_revenue::numeric(20,2));
---

- Bringing data in monetary columns to a logical form (dividing by 1,000,000 and rounding):
---
UPDATE public.all_sessions
SET product_price = ROUND(product_price/1000000, 2);
UPDATE public.all_sessions
SET product_revenue = ROUND(product_revenue/1000000, 2);
UPDATE public.all_sessions
SET item_revenue = ROUND(item_revenue/1000000, 2);
UPDATE public.all_sessions
SET total_transaction_revenue = ROUND(total_transaction_revenue/1000000, 2);
UPDATE public.all_sessions
SET transaction_revenue = ROUND(transaction_revenue/1000000, 2);
---
UPDATE public.analytics
SET revenue = ROUND(revenue/1000000, 2);
UPDATE public.analytics
SET unit_price = ROUND(unit_price/1000000, 2);
--- 

- Removing extra spaces from strings (one of the executed queries for example):
---
UPDATE public.products
SET name = TRIM (both ' ' FROM name);
---

- Checking a difference between sales_by_sku and sales_report tables. The query returns rows that don't exist in sales_report (8 mismatches):
---
SELECT sr.product_sku, 
    sr.total_ordered, 
    sbs.product_sku, 
    sbs.total_ordered
FROM sales_report AS sr
FULL OUTER JOIN sales_by_sku AS sbs
ON sr.product_sku = sbs.product_sku
WHERE sr.product_sku IS NULL;
---

- Checking duplicate records between all_sessions and analytics tables:
--- 
SELECT *
FROM all_sessions AS al
JOIN analytics AS an ON
	al.fullvisitor_id = an.fullvisitor_id 
	AND al.visit_id = an.visit_id 
	AND al.date = an.date 
	AND al.time_on_site = an.time_on_site 
	AND al.channel_grouping = an.channel_grouping 
	AND al.page_views = an.page_views;
---

- Connecting tables by adding primary and foreign keys:
 -- verify uniqueness and not null values (one of the executed queries for example):
---
SELECT DISTINCT product_sku
FROM public.products;
---
SELECT *
FROM public.products
WHERE product_sku IS NULL; 
---

 -- adding a primary key to the table:
---
ALTER TABLE public.products
ADD PRIMARY KEY (product_sku);
---

 -- adding a foreign key to the table:
---
ALTER TABLE public.sales_report
ADD FOREIGN KEY (product_sku) REFERENCES public.products (product_sku);
---

 - Connecting sales_by_sku table and adding a foreign key in the case when some rows don't exist:
---
INSERT INTO public.products (product_sku)
SELECT DISTINCT product_sku
FROM sales_by_sku
WHERE product_sku NOT IN (
	SELECT product_sku 
	FROM public.products);
---
ALTER TABLE public.sales_by_sku
ADD FOREIGN KEY (product_sku) REFERENCES public.products (product_sku);
---

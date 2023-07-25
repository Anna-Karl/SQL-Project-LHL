Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

SQL Queries:

```
WITH level_revenue_city_CTE AS (
	SELECT city, total_transaction_revenue
	FROM all_sessions
	WHERE city NOT IN ('(not set)', 'not available in demo dataset'))
	
SELECT city,
    SUM(total_transaction_revenue) AS revenue_city,
	RANK () OVER (ORDER BY SUM(total_transaction_revenue) DESC) AS rank_city
FROM level_revenue_city_CTE
WHERE total_transaction_revenue IS NOT NULL
GROUP BY city
ORDER BY rank_city;
```
```
SELECT country, city,
    SUM(total_transaction_revenue) AS revenue_country,
	RANK () OVER (ORDER BY SUM(total_transaction_revenue) DESC) AS rank_country
FROM all_sessions
WHERE total_transaction_revenue IS NOT NULL
GROUP BY country, city
ORDER BY rank_country;
```

Answer:

The highest level of transaction revenues between cities: 1. San Francisco, 2.Sunnyvale, 3.Atlanta, 4.Palo Alto, 5.Tel Aviv-Yafo, 6.New York, 7.Mountain View, 8. Los Angeles, 9. Chicago, 10.Sydney.

The highest level of transaction revenues between countries: 1. United States, 2.Israel, 3. Australia, 4. Canada, 5.Switzerland. For further analysis, attention should be paid to the identification of cities in the United States that take a significant part of transaction revenue. 


**Question 2: What is the average number of products ordered from visitors in each city and country?**

SQL Queries:

```
SELECT  
	CASE
		WHEN country = '(not set)' THEN NULL
		ELSE country
	END,
	ROUND(AVG(product_quantity),2) AS average_products 
FROM all_sessions
WHERE product_quantity IS NOT NULL
GROUP BY country 
ORDER BY average_products DESC;
```
```
SELECT 
	CASE
		WHEN city = '(not set)' THEN NULL
		WHEN city = 'not available in demo dataset' THEN NULL
	 	ELSE city
	 END,
	 ROUND(AVG(product_quantity)) AS average_products 
FROM all_sessions
WHERE product_quantity IS NOT NULL
GROUP BY city
ORDER BY average_products  DESC;
```

Answer:

The average number of products ordered from visitors in Spain is 10, United States - 4, and other countries show 1 on average.

The average number of products ordered from visitors by city was distributed as follows: Madrid - 10, Salem - 8, Atlanta - 4, Houston - 2, and other cities show 1 on average.  It should be mentioned about cities about which the information is missed. They also have a high average number, which is important for further analysis.


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

SQL Queries:

```
SELECT a.product_category, a.country, a.city,
	COUNT(a.product_category) as count_category
FROM (
SELECT DISTINCT * 
FROM analytics
WHERE units_sold IS NOT NULL
) as alt
JOIN all_sessions a
USING(fullvisitor_id)
GROUP BY a.product_category, a.country, a.city
ORDER BY count_category DESC, country, city;
```
```
SELECT country,product_category, COUNT(*)
FROM all_sessions
GROUP BY product_category , country
ORDER BY COUNT(*) DESC;
```

Answer:

The result of query 1 shows that most often such categories as pet accessories, accessories, apparel, nest, notebooks & journals, bags, and housewares were sold in cities of the United States which are not identified. The city of Hong Kong bought a lot of electronic accessories. The other cities do not have enough purchase records to draw any confident conclusions.

The result of query 2 shows that such categories as men's t-shirts, Youtube merchandise, electronics, apparel, and Google brand most commonly occur in orders from the United States. Also, the United Kingdom and India bought products of Youtube merchandise.


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

SQL Queries:

```
WITH all_sessions_cte AS (
    	SELECT 
        	CASE
           	 WHEN a.country = '(not set)' THEN NULL
             ELSE a.country
        	END AS country,
       		CASE
           	 WHEN a.product_name = '(not set)' THEN NULL
             ELSE a.product_name
        	END AS product_name,
            a.product_sku
    	FROM all_sessions a),
total_orders_by_product AS (
        SELECT country, product_name, 
               SUM(p.ordered_quantity) AS ordered_quantity_sum
        FROM all_sessions_cte alc
        JOIN products p ON p.product_sku = alc.product_sku
        WHERE alc.product_name IS NOT NULL
        AND alc.country IS NOT NULL
        AND p.ordered_quantity > 0
        GROUP BY alc.country, alc.product_name
)
SELECT *
FROM total_orders_by_product
ORDER BY ordered_quantity_sum DESC
LIMIT 10;
```
```
WITH all_sessions_cte AS (
    	SELECT 
        	CASE
           	 WHEN a.city = 'not available in demo dataset' THEN NULL
		     WHEN a.city = '(not set)' THEN NULL
             ELSE a.city
        	END AS city,
       		CASE
           	 WHEN a.product_name = '(not set)' THEN NULL
             ELSE a.product_name
        	END AS product_name,
            a.product_sku
    	FROM all_sessions a),
total_orders_by_product AS (
        SELECT city, product_name, 
               SUM(p.ordered_quantity) AS ordered_quantity_sum
        FROM all_sessions_cte alc
        JOIN products p ON p.product_sku = alc.product_sku
        WHERE alc.product_name IS NOT NULL
        AND alc.city IS NOT NULL
        AND p.ordered_quantity > 0
        GROUP BY alc.city, alc.product_name
)
SELECT *
FROM total_orders_by_product
ORDER BY ordered_quantity_sum DESC
LIMIT 10;
```

Answer:

Based on results by countries, 'YouTube Custom Decals' emerges as the top-selling product (United States, India). 'Google Kick Ball' ranks first in sales in the United States. Also, it should be noted that 'Nest Security Camera', 'Youtube Bootle Infuser', and  'Google Water Bottle' are top-ten selling products in the United States.

Based on results by cities, we can conclude that 'Google Kick Ball' was the top-selling product in most of the cities of the United States. However, it is noteworthy that the product with the highest overall sales volume is 'Nest Cam Indoor Security Camera - USA'. Most of these products were bought in such cities as Moutain View, Chicago, New York, and San Francisco.


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

```
SELECT country, 
    SUM(total_transaction_revenue) AS transaction_revenue,
	ROUND(SUM(total_transaction_revenue)/SUM(SUM(total_transaction_revenue)) OVER () * 100, 2) AS revenue_country
FROM all_sessions
WHERE total_transaction_revenue IS NOT NULL
GROUP BY country
ORDER BY transaction_revenue DESC;
```
```
SELECT city, 
    SUM(total_transaction_revenue) AS transaction_revenue,
	ROUND(SUM(total_transaction_revenue)/SUM(SUM(total_transaction_revenue)) OVER () * 100, 2) AS revenue_city
FROM all_sessions
WHERE total_transaction_revenue IS NOT NULL 
AND city NOT IN ('not available in demo dataset','(not set)')
GROUP BY city
ORDER BY transaction_revenue DESC;
```

Answer:

The obtained results confirm the conclusions in the previous questions: the most significant part of the revenue belongs to the United States (92%). Then the distribution goes by country, respectively: Israel (4%), Australia (2,5%), Canada (1%), and Switzerland (0.1%).

Results are more diverse when it comes to the cities, where San Francisco is on the top of the revenue (19%), followed by Sunnyvale (12%), Atlanta (10%), Palo Alto (7,5%), Tel Aviv-Yafo (7,3%), New York (7,3%).

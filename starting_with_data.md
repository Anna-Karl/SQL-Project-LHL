# Question 1: What is the main sources that brought traffic to the website?

SQL Queries: 
---
SELECT channel_grouping,
	COUNT(DISTINCT(fullvisitor_id)),
	ROUND(COUNT(DISTINCT(fullvisitor_id)) / SUM(COUNT(DISTINCT(fullvisitor_id))) OVER () * 100, 2) AS rate_unique_visits
FROM all_sessions
GROUP BY channel_grouping
ORDER BY rate_unique_visits DESC;
---

Answer: 
58% of the traffic came through Organic Search (Google, Bing). 20% and 17% of users came to the website directly and by referral, respectively. Paid Search accounts for 3% of the total traffic.


# Question 2: What is the most viewed products on the website?

SQL Queries:
---
SELECT product_name, 
	COUNT(DISTINCT(fullvisitor_id)) AS unique_visit
FROM all_sessions
GROUP BY product_name
ORDER BY unique_visit DESC;
---

Answer:
The most viewed product is 'Google men's cotton short' with almost 300 views, next are the following examples of Youtube merchandise, such as bottles, cups, decals, tees, and caps.


# Question 3: What are the most frequently purchased products by visitors from a specific country?

SQL Queries:
---
SELECT al.country, al.product_name, COUNT(*) AS purchase_count
FROM all_sessions al
JOIN analytics an ON al.visit_id = an.visit_id
WHERE an.units_sold > 0
GROUP BY al.country, al.product_name
ORDER BY purchase_count DESC;
---

Answer:
According to the received result, 'Slip&Slender lip balm" is the most frequently purchased product in the United States. 'Google men's pullover' is in second place in terms of frequency of purchases, and 'Google backpack' closes the top three.


# Question 4: How many unique visitors were on the website for the period and how many of them made at least one purchase? 

SQL Queries:
---
SELECT COUNT(DISTINCT fullvisitor_id) as visitor_count, 
	(SELECT COUNT(DISTINCT fullvisitor_id)
	 		FROM analytics
			WHERE units_sold > 0) as purchase,
    MIN(date) as period_start, 
    MAX(date) as period_end
FROM analytics;
---

Answer:
Between 2017-05-01 and 2017-08-01, the website was visited by 120018 unique users. Only 16063 of them made a purchase.

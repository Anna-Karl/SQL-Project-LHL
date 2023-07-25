# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
The goal of this project is to extract, clean, and gain insights from a dataset and analyze it using SQL. The ecommerce dataset consists  tables: all_sessions, analytics, products, sales_by_sku, and sales_report. The project involves loading, extracting, and transforming the data, as well as performing various analyses to answer specific questions and uncover meaningful patterns and trends in the revenue and sales of an online store.

## Process
### Loading csv Files into Database
- creating a database and importing data from csv files into pgAdmin
### Exploring data
- checking columns for data types and unexpected values, understanding the structure and contents of each table
### Data Cleaning
- removing null and duplicate values, transforming data types, adding primary and foreign keys
### Analyzing data
- using queries and various SQL functions, aggregations, and filters to find trends in sales and revenue
### QA data
- identifying and describing risk areas, executing a QA process to address them, and validating the accuracy of results

## Results
Based on the data, I was able to answer a series of business questions that might drive data-driven decision-making and strategic planning to optimize business performance and maximize revenue. 
- The highest level of revenues between countries: 1. United States, 2.Israel, 3. Australia, 4. Canada, 5.Switzerland.
- The average number of products ordered by visitors from Madrid - 10, Salem - 8, Atlanta - 4, Houston - 2, respectively.
- The categories, selling best in cities of the United States, are pet accessories, accessories, apparel, nest, notebooks & journals, bags, and housewares.
- 'YouTube Custom Decals' emerges as the top-selling product in the United States and India, and 'Google Kick Ball' was the top-selling product in most of the cities of the United States 
- The most significant part of the revenue comes from the United States (92%). San Francisco is at the top of the revenue (19%), followed by Sunnyvale (12%), Atlanta (10%).
- 58% of the traffic came through Organic Search (Google, Bing). 20% and 17% of users came to the website directly and by referral, respectively. Paid Search accounts for 3% of the total traffic.
- The most viewed product is 'Google men's cotton shorts' with almost 300 views, next are the following examples of Youtube merchandise, such as bottles, cups, decals, tees, and caps.
- 'Slip&Slender lip balm" is the most frequently purchased product in the United States. 'Google men's pullover' is in second place in terms of frequency of purchases, and 'Google backpack' closes the top three.
- Between 2017-05-01 and 2017-08-01, the website was visited by 120018 unique users. Only 16063 of them made a purchase.

## Challenges 
- The quality of the raw data: the tables have many missing or misplaced values, as well as duplicate rows or similar value columns.
- Deleting data: lack of information about further filling out of the dataset cannot allow to drop empty columns, or removing some values and columns might affect the results.
- Establishing relationships between tables:  missing values in primary key columns and absence of a suitable key to establish a proper connection.
- Comprehension of the data and the dependencies between them: required additional research to understand the data accurately.

## Future Goals
If I were given more time, my focus would be on: 
- cleaning and processing the raw data better 
- data quality assurance 
- checking tables for outliers and values that do not make sense

Furthermore, I would conduct a deeper analysis of business questions and, perhaps, perform a statistical analysis of the data. 
Examples of issues that could also be analyzed to optimize sales, procurement, and storage of goods:
- average time/page views that person spends on the website before making a purchase
- day and time of making most purchases
- the impact of the number of visits on revenue
- total transactions based on channel grouping
- channel from which most visitors bounce on the website
- products with the lowest/highest restock time
- products with a low amount of orders and a high stock level
- products with lowest/highest ratio, sentiment score, sentiment magnitude
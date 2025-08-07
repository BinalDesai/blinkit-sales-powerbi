Blinkit Analysis

Project Overview
This project involves analyzing Blinkit product sales data to uncover insights related to sales performance, customer ratings, and outlet characteristics. Using SQL for data cleaning and aggregation, and Power BI for visualization, the goal is to help business stakeholders understand key sales drivers and optimize strategies.

Data Source
The dataset contains Blinkit transactional sales data including:
•	Item details (Type, Fat Content)
•	Sales amount
•	Customer ratings
•	Outlet information (Location, Size, Establishment Year, Type)
See all the data imported:
select * from blinkit_data;

DATA CLEANING
Cleaning the Item_Fat_Content field ensures data consistency and accuracy in analysis.The presence of multiple variations of the same category (e.g., LF, low fat vs. Low Fat) can cause issues in reporting, aggregations, and filtering. By standardizing these values, we improve data quality, making it easier to generate insights and maintain uniformity in our datasets.

UPDATE blinkit_data
SET Item_Fat_Content =
CASE
	     WHEN Item_Fat_Content IN ('LF', 'low fat') THEN 'Low Fat'
 	     WHEN Item_Fat_Content = 'reg' THEN 'Regular'
    ELSE Item_Fat_Content
END;

After executing this query, check if the data has been cleaned or not using the query below:

SELECT DISTINCT Item_Fat_Content FROM blinkit_data;

 

   KPI’s
1.	TOTAL SALES:
SELECT CAST(SUM(Total_Sales) / 1000000.0 AS DECIMAL(10,2)) AS Total_Sales_Million
FROM blinkit_data;

Example Output:
 

2.	AVERAGE SALES:
SELECT CAST(AVG(Total_Sales) AS INT) AS Avg_Sales
FROM blinkit_data;
Example Output:
 
3.	NO OF ITEMS:
SELECT COUNT(*) AS No_of_Orders
FROM blinkit_data;
 

4.Average Rating

select cast(avg(rating) as decimal(10,2))as avg_rating from blinkit_data;

 

Granular Requirements
1.Total Sales by Fat Content

Objective: Analyze the impact of fat content on total sales.
Additional KPI Metrics: Assess how other KPIs (Average Sales, Number of Items, Average Rating) vary with fat content.

SELECT `Item Fat Content`, 
CAST(SUM(Sales) AS DECIMAL(10,2)) AS Total_Sales,
CAST(avg(Sales) AS DECIMAL(10,1)) AS Avg_Sales,
COUNT(*) AS No_of_Orders,
cast(avg(rating) as decimal(10,2))as Avg_rating
FROM blinkit_data
GROUP BY `Item Fat Content`
ORDER BY Total_Sales DESC;

 
2. Total Sales by Item Type:
Objective: Identify the performance of different item types in terms of total sales.
Additional KPI Metrics: Assess how other KPIs (Average Sales, Number of Items, Average Rating) vary with item content.
SELECT 
  `Item Type`,
  CAST(SUM(Sales) AS DECIMAL(10,2)) AS Total_Sales,
  CAST(AVG(Sales) AS DECIMAL(10,1)) AS Avg_Sales,
  COUNT(*) AS No_of_Orders,
  CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating
FROM blinkit_data
GROUP BY `Item Type`
ORDER BY Total_Sales DESC;

 

3.Fat Content by Outlet for Total Sales:
Objective: Compare total sales across different outlets segmented by fat content.
Additional KPI Metrics: Assess how other KPIs (Average Sales, Number of Items, Average Rating) vary with fat content.
SELECT 
  `Outlet Location Type`,
  CAST(IFNULL(SUM(CASE WHEN `Item Fat Content` = 'Low Fat' THEN Sales END), 0) AS DECIMAL(10,2)) AS Low_Fat,
  CAST(IFNULL(SUM(CASE WHEN `Item Fat Content` = 'Regular' THEN Sales END), 0) AS DECIMAL(10,2)) AS Regular
FROM blinkit_data
GROUP BY `Outlet Location Type`
ORDER BY `Outlet Location Type`;

 
4.  Total Sales by Outlet Establishment:
 Objective: Evaluate how the age or type of outlet establishment influences total sales.
   SELECT 
  `Outlet Establishment Year`,
  CAST(SUM(Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY `Outlet Establishment Year`
ORDER BY `Outlet Establishment Year` ASC;
	 

BUSINESS REQUIREMENT
Chart’s Requirements
5.	Percentage of Sales by Outlet Size:
Objective: Analyze the correlation between outlet size and total sales.
SELECT
  `Outlet Size`,
  CAST(SUM(Sales) AS DECIMAL(10,2)) AS Total_Sales,
  CAST((SUM(Sales) * 100.0 / SUM(SUM(Sales)) OVER()) AS DECIMAL(10,2)) AS Sales_Percentage
FROM blinkit_data
GROUP BY `Outlet Size`
ORDER BY Total_Sales DESC;

 
6.	Sales by Outlet Location:
Objective: Assess the geographic distribution of sales across different locations.

SELECT `Outlet Location Type`,
  CAST(SUM(Sales) AS DECIMAL(10,2)) AS Total_Sales,
   CAST((SUM(Sales) * 100.0 / SUM(SUM(Sales)) OVER()) AS DECIMAL(10,2)) AS Sales_Percentage,
  CAST(AVG(Sales) AS DECIMAL(10,1)) AS Avg_Sales,
  COUNT(*) AS No_Of_Items,
  CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating
FROM blinkit_data
GROUP BY `Outlet Location Type`
ORDER BY Total_Sales DESC;

 
7. All Metrics by Outlet Type:
Objective: Provide a comprehensive view of all key metrics (Total Sales, Average Sales, Number of Items, Average Rating) broken down by different outlet types.
SELECT `Outlet Type`,
  CAST(SUM(Sales) AS DECIMAL(10,2)) AS Total_Sales,
   CAST((SUM(Sales) * 100.0 / SUM(SUM(Sales)) OVER()) AS DECIMAL(10,2)) AS Sales_Percentage,
  CAST(AVG(Sales) AS DECIMAL(10,1)) AS Avg_Sales,
  COUNT(*) AS No_Of_Items,
  CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating
FROM blinkit_data
GROUP BY `Outlet Type`
ORDER BY Total_Sales DESC;

 


# Superstore SQL Analysis - Complete SQL Project

**Project Title**: Superstore Analysis  
**Level**: Beginner  
**Database**: Superstore  

## Description  
Superstore sales dataset: 500+ rows of orders with details like products, categories, regions, sales, profit, discounts, and shipping modes. Ideal for business analysis and visualization projects.

## Project Structure  
**Database Creation**: The project starts by creating a database named sales.  
**Table Creation**: A table named superstore is created to store Superstore product information. The table structure includes columns as Row ID, Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Customer Name, Segment, Country/Region, City, State/Province, Postal Code, Region, Product ID, Category, Sub-Category, Product Name, Sales, Quantity, Discount, Profit.


CREATE TABLE superstore (
    Row_ID INT,
    Order_ID VARCHAR(20),
    Order_Date DATE,
    Ship_Date DATE,
    Ship_Mode VARCHAR(50),
    Customer_ID VARCHAR(20),
    Customer_Name VARCHAR(100),
    Segment VARCHAR(50),
    Country VARCHAR(50),
    City VARCHAR(100),
    State VARCHAR(100),
    Postal_Code INT,
    Region VARCHAR(50),
    Product_ID VARCHAR(50),
    Category VARCHAR(50),
    Sub_Category VARCHAR(50),
    Product_Name VARCHAR(255),
    Sales DECIMAL(10,2),
    Quantity INT,
    Discount DECIMAL(5,2),
    Profit DECIMAL(10,2)
);


# 2. Data Analysis & Findings  
The following SQL queries were developed to answer specific business questions:



** 1. Find all orders whose sales are above the average sales.
SELECT * FROM superstore
WHERE sales > (SELECT AVG(sales) FROM superstore);
```

** 2. List customers who have placed more orders than the average number of orders per customer.

SELECT Customer_ID, COUNT(*) AS total_orders
FROM superstore
GROUP BY Customer_ID
HAVING COUNT(*) >
      (SELECT AVG(order_count)
       FROM (SELECT Customer_ID, COUNT(*) AS order_count
             FROM superstore
             GROUP BY Customer_ID) AS c);


** 3. Get products whose profit is greater than the average profit of their category.**
```sql
SELECT *
FROM superstore s
WHERE s.profit > (
    SELECT AVG(profit)
    FROM superstore
    WHERE Category = s.Category
);
** 4 . Find all products belonging to categories that have return rate above 10%**
```sql
SELECT *
FROM superstore
WHERE category IN (
    SELECT category
    FROM superstore
    WHERE returned = 'Yes'
    GROUP BY category
    HAVING COUNT(returned)/COUNT(*) > 0.10
);

** 5. Retrieve all columns for orders from the 'South' region, limited to the first 10 rows. **

SELECT * FROM superstore 
WHERE Region='South' 
ORDER BY Order_Date 
LIMIT 10;


** 6. List the top 5 products by sales amount, including product name and sales.

SELECT Product_Name, Sales 
FROM superstore 
ORDER BY Sales DESC 
LIMIT 5;

** 7. Find all orders where profit is negative and quantity is more than 5.

SELECT Order_ID, Product_Name, Profit, Quantity 
FROM superstore 
WHERE profit < 0 AND Quantity > 5
ORDER BY Profit ASC; 

** 8. Get customer names and segments for orders shipped via 'Standard Class'.

SELECT Customer_Name, Segment, Ship_Mode 
FROM superstore 
WHERE Ship_Mode = 'Standard Class';

** 9. Filter orders from California (State = 'California') between January 1, 2015, and December 31, 2015.

SELECT Order_Date, Order_ID, COUNT(*) number_oforder, Customer_Name 
FROM superstore 
WHERE State = 'California' 
  AND Order_Date BETWEEN '2015-01-01' AND '2015-12-31' 
GROUP BY Order_ID, Order_Date, Customer_Name 
ORDER BY Order_Date;


** 10. Calculate total sales and average profit by category.

SELECT category, SUM(sales) total_sales, AVG(Profit) average_profit 
FROM superstore 
GROUP BY Category 
ORDER BY average_profit DESC;

** 11. Find regions with total profit greater than 5000, including count of orders.

SELECT Region, SUM(Profit) total_profit, COUNT(*) 
FROM superstore  
GROUP BY Region 
HAVING SUM(Profit) > 5000;


** 12. Get monthly total sales for 2015, grouped by year-month.

SELECT 
    DATE_FORMAT(Order_Date, '%Y-%m') AS Month,
    SUM(Sales) AS Monthly_Sales
FROM superstore
WHERE YEAR(Order_Date) = 2015
GROUP BY DATE_FORMAT(Order_Date, '%Y-%m')
ORDER BY Month;
```

** 13. Count the number of orders per ship mode, only for consumer segment.

SELECT Ship_Mode, COUNT(*) Total_order 
FROM superstore 
WHERE Segment='Consumer' 
GROUP BY Ship_Mode 
ORDER BY Total_order ASC;


** 14. Find sub-categories with average discount above 0.4 and total quantity sold over 100.

SELECT Sub_Category, AVG(Discount) average_Discount, SUM(Quantity) 
FROM superstore 
GROUP BY Sub_Category 
HAVING AVG(Discount) > 0.4 AND SUM(Quantity) > 100;




** 15. Rank products by sales within each category (using window function).

SELECT *, ROW_NUMBER() OVER (PARTITION BY Category ORDER BY Sales DESC) rank_products 
FROM superstore;
```

** 16. Find the 5th highest Salary

SELECT DISTINCT MAX(Sales) 
FROM (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY Category ORDER BY Sales DESC) rank_products 
    FROM superstore
) t 
WHERE rank_products = 332;
```

** 17. Find the top-performing customer (by total profit) in each region using a subquery.
 
SELECT DISTINCT *
FROM (
  SELECT Region, Customer_ID, SUM(Profit) AS Total_Profit,
         RANK() OVER (PARTITION BY Region ORDER BY SUM(Profit) DESC) AS Customer_Rank 
  FROM superstore 
  GROUP BY Region, Customer_ID
) ranked_customers 
WHERE Customer_Rank = 1;
 

** 18. Identify orders where sales are above the average sales for that sub-category and ship mode. 
 
SELECT Order_ID, Sub_Category, Ship_Mode, Sales 
FROM superstore s1 
WHERE Sales > (
  SELECT AVG(Sales) 
  FROM superstore s2 
  WHERE s2.Sub_Category = s1.Sub_Category 
    AND s2.Ship_Mode = s1.Ship_Mode
)
ORDER BY Sales DESC 
LIMIT 10;
 

** 19. Find the cumulative profit by order date for the 'Technology' category, reset per region (using window function).
 
SELECT
    Order_Date,
    Region,
    Profit,
    SUM(Profit) OVER (
        PARTITION BY Region
        ORDER BY Order_Date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS cumulative_profit
FROM superstore
WHERE Category = 'Technology'
ORDER BY Region, Order_Date;
 # Conclusion 
 This Superstore SQL Analysis Project successfully bridges the gap between technical SQL skills and practical business applications. By working through realistic business scenarios, it demonstrates how SQL can transform raw sales data into strategic business intelligence. The project not only showcases technical proficiency but also highlights the critical thinking required for effective data analysis in a business context.

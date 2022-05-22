## **Shopify Fall 2022 Data Science Intern Challenge**
### *By Anjali Chauhan*
##### Date: 16/05/2022

#### *(Please refer to the code in the Jupyter notebook (.ipynb, .pdf) available in the repo)*

#### Question 1:
Given some sample data, write a program to answer the following: (https://docs.google.com/spreadsheets/d/16i38oonuX1y1g7C_UAmiK9GkY7cS-64DfiDMNiR41LM/edit#gid=0)

On Shopify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis.

***(i) Think about what could be going wrong with our calculation. Think about a better way to evaluate this data.***

**Answer**: 

- AOV calculated in the question: Total revenue/number of orders = `df.order_amount.sum()/len(df.order_id.unique())`

- We see the average of `order_amount` is 3145.128 according to the above formula. We also see below why this is not a robust metric.

-  The max value of order_amount is 704000 with min value of 90 and 3rd quartile of 390 (way smaller than the max value).

- Since mean is very sensitive to extreme values, the calculated value is not a very good AOV estimate due to skewness and that's why this amount is large.

- Usually in this case, we could check for data quality and make sure we don't have any errors in the data. If the extreme data points are data entry errors we can drop these rows and use the mean to calculate AOV.

- If these extreme data points are not incorrect data points, we should not be dropping these rows as that would mean excluding important subset of information. We could use median instead which is robust to outliers. 

After investigating more:

- We see that all of the orders of size 2000 occurred from the same user_id: 607 and from the same shop_id: 42.

- The order_amount is exactly the same too for  each of these orders about 704000.

- Here's one instance of multiple identical transactions: From the first 5 rows, we can see that all the orders were made at exactly 4AM. on 2017-03-02. 

- This might be a case of duplicate entries in the dataset, or this might just be the case where the customer might be buying sneakers in bulk.

- Another observation: there is some inconsistency in the following two metrics in the top 5 and the bottom 5 shops based on their average order amount and total revenue.

| total_items | avg_order_amt | max_order_amt | per_item_amt |
|---:|---:|---:|---:|
| 1 | 417.364481 | 25725.0 | 25725.0 |
| 2 | 750.215066 | 51450.0 | 25725.0 |
| 3 | 1191.076514 | 77175.0 | 25725.0 |
| 4 | 947.686007 | 102900.0 | 25725.0 |
| 5 | 759.350649 | 1760.0 | 352.0 |
| 6 | 17940.000000 | 154350.0 | 25725.0 |
| 8 | 1064.000000 | 1064.0 | 133.0 |
| 2000 | 704000.000000 | 704000.0 | 352.0 |

- Based on the df above, we see some of these max_order_amt are way above the average. We would never expect an average pair of shoes to cost 25725. This in addition to the duplicate transactions questions the correctness of these entries.

- Therefore, we will need a robust metric like median that is not sensitive to outliers if we want to keep these observations (if these are not errors).


***(ii) What metric would you report for this dataset?***

**Answer**: After a thorough exploration (refer to the .ipynb file), we can say that the best metric option would be to use median of the order_amount.

***(iii) What is its value?***

**Answer**:  $284

#### Question 2
For this question you’ll need to use SQL. Follow this link (https://www.w3schools.com/SQL/TRYSQL.ASP?FILENAME=TRYSQL_SELECT_ALL) to access the data set required for the challenge. Please use queries to answer the following questions. Paste your queries along with your final numerical answers below.

***(i) How many orders were shipped by Speedy Express in total?***
**Answer**: 54 orders were shipped by Speedy Express in total

```
SELECT o.OrderID, o.ShipperID, s.ShipperID, s.ShipperName, COUNT(*) FROM [Orders] o
LEFT JOIN [Shippers] s ON o.ShipperID = s.ShipperID
WHERE s.ShipperName = 'Speedy Express'
```

***(ii) What is the last name of the employee with the most orders?***
**Answer**: Peacock is the last name of the employee with the most orders, about 40

```
SELECT e.LastName, COUNT(DISTINCT(o.OrderID)) AS total_orders FROM  [Employees] e
LEFT JOIN [Orders] o
ON e.EmployeeID =  o.EmployeeID
GROUP BY e.LastName
ORDER BY  total_orders DESC
LIMIT 1
```

***(iii) What product was ordered the most by customers in Germany?***
**Answer**: Camembert Pierrot was the product ordered the most by customers in Germany.

```
WITH ProductQuantity AS (
    SELECT Orders.OrderID, Products.ProductName, OrderDetails.Quantity
    FROM OrderDetails, Orders
    JOIN Customers ON Customers.CustomerID = Orders.CustomerID
    JOIN Products ON OrderDetails.ProductID=Products.ProductID
    WHERE Country =='Germany' 
),

ProductOrders as (
    SELECT ProductName, Quantity, COUNT(*) as 'Orders'
    FROM ProductQuantity
    GROUP BY ProductName
)

SELECT ProductName, Quantity, Orders, (Orders*Quantity) AS TotalQuantityOrdered
FROM ProductOrders
ORDER BY TotalQuantityOrdered DESC
LIMIT 1;

```
|ProductName | Quantity |Orders | TotalQuantityOrdered|
|:---:|:---:|:---:|:--:|
|Camembert Pierrot|40|300|12000|

## **Shopify 2022 Data Science Intern Challenge**
### *By Anjali Chauhan*

Please complete the following questions, and provide your thought process/work. You can attach your work in a text file, link, etc. on the application page. Please ensure answers are easily visible for reviewers!

#### Question 1:
Given some sample data, write a program to answer the following: click here to access the required data set

On Shopify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis.

(i) Think about what could be going wrong with our calculation. Think about a better way to evaluate this data.

**Answer**: The wrong average was calculated using this method: total of all order values/ number of order_values. This is wrong because the formula didn't consider the fact that an order can have multiple items.

(ii) What metric would you report for this dataset?

**Answer**: The correct approach would be to divide the total of all order_values by the sum of total_items. By following this method, we would consider the fact that an order can have multiple items.

(iii) What is its value?

**Answer**:  $357.92

#### Question 2
For this question you’ll need to use SQL. Follow this link to access the data set required for the challenge. Please use queries to answer the following questions. Paste your queries along with your final numerical answers below.

(i) How many orders were shipped by Speedy Express in total?
**Answer**: 54 orders were shipped by Speedy Express in total

```
SELECT o.OrderID, o.ShipperID, s.ShipperID, s.ShipperName, COUNT(*) FROM [Orders] o
LEFT JOIN [Shippers] s ON o.ShipperID = s.ShipperID
WHERE s.ShipperName = 'Speedy Express'
```

(ii) What is the last name of the employee with the most orders?
**Answer**: Peacock is the last name of the employee with the most orders, about 40

```
SELECT e.LastName, COUNT(DISTINCT(o.OrderID)) AS total_orders FROM  [Employees] e
LEFT JOIN [Orders] o
ON e.EmployeeID =  o.EmployeeID
GROUP BY e.LastName
ORDER BY  total_orders DESC
LIMIT 1
```

(iii) What product was ordered the most by customers in Germany?
**Answer**: Boston Crab Meat was the product ordered the most by customers in Germany, about 160. In this problem, product most ordered is defined as total quantity of the product ordered and not how many times it was ordered.

```
SELECT d.OrderID, p.ProductID, p.ProductName, SUM(d.Quantity) AS TotalQuantity FROM [Customers] c
LEFT JOIN [Orders] o
ON c.CustomerID = o.CustomerID
LEFT JOIN [OrderDetails] d
ON o.OrderID = d.OrderID
LEFT JOIN [PRODUCTS] p
ON d.ProductID = p.ProductID
WHERE c.Country = 'Germany'
GROUP BY p.ProductName
ORDER BY TotalQuantity DESC
LIMIT 1
```

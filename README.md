## **Shopify Fall 2022 Data Science Intern Challenge**
### *By Anjali Chauhan*
##### Date: 17/05/2022

#### *(Please refer to the code in the Jupyter notebook (.ipynb, .pdf) available in the repo)*

#### Question 1:
Given some sample data, write a program to answer the following (https://docs.google.com/spreadsheets/d/16i38oonuX1y1g7C_UAmiK9GkY7cS-64DfiDMNiR41LM/edit#gid=0)

On Shopify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis.

***(i) Think about what could be going wrong with our calculation. Think about a better way to evaluate this data.***

**Answer**: $3145.13 is not the AOV that we want. We get this value by dividing total sum of order_amount by number of orders. This is wrong because the formula didn't consider the fact that an order can have multiple items. For better readability and interpretability we can create a new column called `item_price` which will be order_amount/total_items. This is help us easily compare prices of individual sneakers sold by different shops.

Also, we see 17 outliers from shop 42 which skew the average therefore we need to evaluate whether these values are simply due to data entry error or these represent correct information. This shop gets large number of orders (2000 total items in some cases). Price for one sneaker at this shop is $352 which seems affordable compared to shop 78 and this might be the case where some supplier or other merchant is buying in a bulk order. This can be considered as one edge case. Another one is shop 78 selling sneakers for a VERY high price (like $25,725!!!) and is definitely not affordable. This again could be a data entry error or it could be a luxury designer brand or a limited edition autographed sneaker. 

*Do you know Michael Jordan's Air Jordan 4 Starfish (collector's model) is priced at $154,990 and several Nike and Air Jordan sneakers are listed above $100,000 and even above $200,000?*

***(ii) What metric would you report for this dataset?***

**Answer**: The correct approach would be to divide the total of all order_values by the sum of total_items since the metric used in question didn't consider the fact that an order can have multiple items. Due to the presence of outliers, using this metric or AOV will be skewed and would not be reflexive of the orders. Therefore another approach along with AOV would be to use median which is unaffected by the outliers.

***(iii) What is its value?***

**Answer**:  Corrected AOV: $357.92 (close to 75th percentile: $390), Median: $284

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
**Answer**: Gorgonzola Telino was the product ordered the most by customers in Germany, about 5.

```
SELECT p.ProductName, c.Country, COUNT(o.OrderID) AS NumOrders FROM Customers c 
LEFT JOIN Orders o 
ON c.CustomerID = o.CustomerID
LEFT JOIN OrderDetails d
ON o.OrderID = d.OrderID
LEFT JOIN Products p
ON d.ProductID = p.ProductID
WHERE c.Country = 'Germany'
GROUP BY p.ProductName
ORDER BY NumOrders DESC
LIMIT 1;

```

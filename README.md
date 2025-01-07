Description: This repository contains SQL queries designed to analyze various aspects of pizza sales, focusing on revenue generation, popularity, and customer behavior. The queries cover metrics such as total orders, total revenue, most ordered pizzas, and revenue distribution across pizza types and categories.

SQL Queries Included:
Total Number of Orders:
Calculates the total number of pizza orders placed across all time periods.

sql
Copy code
select count(*) as Total_orders from orders;
Total Revenue from Pizza Sales:
Computes the total revenue generated from pizza sales by multiplying pizza price and quantity ordered.

sql
Copy code
select sum(a.price*b.quantity) as Total_sales
from pizzas as a
join orders_details as b 
on a.pizza_id=b.pizza_id;
Highest-Priced Pizza:
Identifies the most expensive pizza based on its price.

sql
Copy code
SELECT a.name, max(b.price) AS pizza_price
FROM pizza_types AS a
JOIN pizzas AS b ON a.pizza_type_id = b.pizza_type_id
GROUP BY a.name
ORDER BY pizza_price DESC
LIMIT 1;
Most Common Pizza Size Ordered:
Analyzes which pizza size is ordered the most by counting the total quantity per size.

sql
Copy code
select a.size,count(b.quantity) as no_of_pizzas
from orders_details as b join pizzas as a on a.pizza_id=b.pizza_id
group by a.size order by no_of_pizzas desc;
Top 5 Most Ordered Pizza Types:
Lists the top 5 most ordered pizza types based on their total quantities.

sql
Copy code
SELECT pizza_types.name,sum(orders_details.quantity)as TOTAL_QTY
FROM orders_details
JOIN pizzas ON pizzas.pizza_id=orders_details.pizza_id
JOIN pizza_types ON pizza_types.pizza_type_id= pizzas.pizza_type_id
group by pizza_types.name
order by TOTAL_QTY DESC
LIMIT 5;
Revenue by Pizza Category:
Calculates the total quantity of each pizza category ordered by summing up the quantities for each category and grouping the results by category.

sql
Copy code
select pizza_types.category ,sum(orders_details.quantity) as Total_qty_by_category
from pizza_types join pizzas on pizzas.pizza_type_id = pizza_types.pizza_type_id
join orders_details on orders_details.pizza_id=pizzas.pizza_id
group by pizza_types.category 
order by  Total_qty_by_category desc;
Orders by Hour of the Day:
Determines the distribution of pizza orders by the hour of the day to understand peak ordering times.

sql
Copy code
select hour(order_time), count(order_id) as orders
from orders 
group by hour(order_time) 
order by orders desc;
Average Number of Pizzas Ordered Per Day:
Groups the orders by date and calculates the average number of pizzas ordered daily.

sql
Copy code
select avg(total_orders) as avg_order_qty from
(SELECT DATE(order_date) AS order_date, sum(orders_details.quantity) AS total_orders
FROM orders join orders_details on orders_details.order_id=orders.order_id
GROUP BY orders.order_date) as qty;
Pizza Category Count:
Provides a count of the number of pizza types in each category.

sql
Copy code
SELECT category, COUNT(name) AS total_count
FROM pizza_types
GROUP BY category
ORDER BY total_count DESC;
Top 3 Most Ordered Pizza Types by Revenue for Each Category:
Calculates the top 3 pizza types with the highest revenue in each category, using window functions to rank the pizza types within each category based on revenue.

sql
Copy code
select category,name,revenue,
rank() over(partition by category order by revenue) as ranks
from
(select pizza_types.name,pizza_types.category, sum(pizzas.price*orders_details.quantity) as revenue
from pizzas
join orders_details on  orders_details.pizza_id = pizzas.pizza_id
join pizza_types on pizza_types.pizza_type_id = pizzas.pizza_type_id
group by pizza_types.category,pizza_types.name
order by revenue) as a;
Key Insights:
Order Trends: Identify patterns of customer behavior like peak ordering hours, most common sizes, and popular pizza types.
Revenue Analysis: Determine the pizza types and categories contributing the most to revenue, helping inform marketing and product offerings.
Pizza Category Performance: Highlight the most and least successful pizza categories based on customer preferences and sales performance.
These queries provide a powerful set of tools for analyzing pizza sales data from multiple perspectives, offering insights that can drive better business decisions.

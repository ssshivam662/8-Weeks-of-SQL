# 8-Weeks-of-SQL

## Case Study #1 - Danny's Diner

Question 1. What is the total amount each customer spent at the restaurant?
Solution.

    select sales.customer_id,sum(menu.price) as total_price from sales
    join menu on
    sales.product_id=menu.product_id
    group by sales.customer_id ;
  
  ![image](https://user-images.githubusercontent.com/61065350/130028165-5536950d-9e1f-46c0-8e2f-3666df50ca1d.png)

Question 2. How many days has each customer visited the restaurant?
Solution.
    
    select customer_id, count(distinct order_date) as number_of_days from sales 
    group by customer_id;
    
   ![image](https://user-images.githubusercontent.com/61065350/130028728-01cd48c9-a380-45ab-ad28-80f3ed5fa25f.png)

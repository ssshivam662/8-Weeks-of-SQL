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
   
Question 3. What was the first item from the menu purchased by each customer?

Solution.

    select sales.customer_id,menu.product_name,min(sales.order_date) as first_visit_date from sales 
    join menu on
    sales.product_id=menu.product_id
    group by customer_id;

   ![image](https://user-images.githubusercontent.com/61065350/130071134-06519080-5a04-461b-a92c-b4340606c027.png)

Question 4 a. What is the most purchased item on the menu?

Solution.    

      SELECT sales.product_id,
       count(sales.product_id) AS number_of_times_purchased
      FROM sales
           JOIN
           menu ON sales.product_id = menu.product_id
     GROUP BY sales.product_id
     ORDER BY number_of_times_purchased DESC
     LIMIT 1;
     
   ![image](https://user-images.githubusercontent.com/61065350/130074716-5b62d325-5bbe-4559-8fc2-3e3aac088598.png)
   
Question 4 b. And how many times was it purchased by all customers?

Solution.

      select customer_id, count(customer_id) from sales
      where product_id=3
      group by customer_id;
        
   ![image](https://user-images.githubusercontent.com/61065350/130081883-e6b4cd0f-9164-47bf-88b1-c885858ad780.png)


        

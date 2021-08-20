# 8-Weeks-of-SQL

## Case Study #1 - Danny's Diner

#### I had great time solving the questions for Danny's imaginary restaurant. I have to admit I took help from medium article 'https://medium.com/analytics-vidhya/8-week-sql-challenge-case-study-week-1-dannys-diner-2ba026c897ab' by Katie Huang for some questions (which used rank) as I am still learning. Hope to learn more and solve more queries in coming time.

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

Approach 1.

    select sales.customer_id,menu.product_name,min(sales.order_date) as first_visit_date from sales 
    join menu on
    sales.product_id=menu.product_id
    group by customer_id;

   ![image](https://user-images.githubusercontent.com/61065350/130071134-06519080-5a04-461b-a92c-b4340606c027.png)
   
Approach 2.

    with orders as(
    select sales.customer_id,menu.product_name,sales.order_date, 
    dense_rank() over(partition by sales.customer_id  
    order by sales.order_date) as rank from
    sales
    join menu
    on sales.product_id=menu.product_id)
    select * from orders
    where rank=1
    group by customer_id,product_name
   ![image](https://user-images.githubusercontent.com/61065350/130247631-f50af48c-4597-4687-adf6-343652fab6f4.png)


Question 4 a. What is the most purchased item on the menu?

Solution.    

      SELECT sales.product_id,
       count(sales.product_id) AS number_of_times_purchased,menu.product_name
      FROM sales
           JOIN
           menu ON sales.product_id = menu.product_id
     GROUP BY sales.product_id
     ORDER BY number_of_times_purchased DESC
     LIMIT 1;
     
   ![image](https://user-images.githubusercontent.com/61065350/130187268-20ad0c1b-5129-4356-84ba-cd5cd12df0b3.png)
   
Question 4 b. And how many times was it purchased by all customers?

Solution.

      select customer_id, count(customer_id) from sales
      where product_id=3
      group by customer_id;
        
   ![image](https://user-images.githubusercontent.com/61065350/130081883-e6b4cd0f-9164-47bf-88b1-c885858ad780.png)
   
Question 5. Which item was the most popular for each customer?

Solution.

     WITH cte AS (
    SELECT sales.customer_id,
           sales.order_date,
           sales.product_id,
           menu.product_name,
           count(sales.product_id),
           dense_rank() OVER (PARTITION BY sales.customer_id ORDER BY count(sales.customer_id) ) AS rank
      FROM sales
           JOIN
           menu ON sales.product_id = menu.product_id
     GROUP BY sales.customer_id,
              sales.product_id
            )
        SELECT *
          FROM cte
         WHERE rank = 1;
         
   ![image](https://user-images.githubusercontent.com/61065350/130192023-888e67df-71cc-4aa4-aa55-776d83d6bfbe.png)


Question 6. Which item was purchased first by the customer after they became a member?

Solution.

      SELECT members.customer_id,
           min(sales.order_date) AS first_order_date_after_joining,
           menu.product_name
      FROM sales
           JOIN
           members ON sales.customer_id = members.customer_id
           JOIN
           menu ON sales.product_id = menu.product_id
     WHERE order_date >= join_date
     GROUP BY members.customer_id;

   ![image](https://user-images.githubusercontent.com/61065350/130189456-3cc608e8-21f9-4b16-8073-d290c195ea4c.png)

Question 7. Which item was purchased just before the customer became a member?

Solution.

      SELECT members.customer_id,
           max(sales.order_date) AS first_order_date_after_joining,
           menu.product_name
      FROM sales
           JOIN
           members ON sales.customer_id = members.customer_id
           JOIN
           menu ON sales.product_id = menu.product_id
     WHERE order_date < join_date
     GROUP BY members.customer_id;
     
   ![image](https://user-images.githubusercontent.com/61065350/130189908-4d1cb842-6bbb-43cc-bdab-12cfa5ded401.png)
   
Question 8. What is the total items and amount spent for each member before they became a member?

Solution.
    
      SELECT members.customer_id,
       count(distinct sales.product_id) as number_of_items,sum(menu.price) as total_price
      FROM sales
           JOIN
           members ON sales.customer_id = members.customer_id
           JOIN
           menu ON sales.product_id = menu.product_id
     WHERE order_date < join_date
     GROUP BY members.customer_id;
   ![image](https://user-images.githubusercontent.com/61065350/130192909-a945a910-4896-45da-966b-8f54d14742e0.png)

Question 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

Solution.

       with points as (
        select sales.customer_id,
        case menu.product_name
            when 'sushi' then menu.price*20
            else menu.price*10
        end points
        from sales
         join menu 
        on sales.product_id=menu.product_id
         )
        select customer_id,sum(points) from points
        group by customer_id;
   ![image](https://user-images.githubusercontent.com/61065350/130197394-de9aa958-6218-456d-b017-6479487997ac.png)

Question 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January? (Good problem for revision**)

Solution.

    with point as (
     SELECT sales.customer_id,members.join_date,sales.order_date,menu.price,menu.product_name,
     case when menu.product_name='sushi'  then menu.price*20
     when sales.order_date>=members.join_date and sales.order_date<datetime(members.join_date, '+6 day') then menu.price*20
     else menu.price*10
     end as point_scheme
      FROM sales 
      join members on
      sales.customer_id=members.customer_id
      join menu on 
      sales.product_id=menu.product_id
      )
      select customer_id,sum(point_scheme) from point 
    where order_date<'2021-01-31'
      group by customer_id;

   ![image](https://user-images.githubusercontent.com/61065350/130253683-faf9095b-d3ad-4fa8-9b38-40b5596f1558.png)

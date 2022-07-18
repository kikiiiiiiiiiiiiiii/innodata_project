# Innodata_project
## Part1 
### 1. Draw an ER model for the 5 five tables created in Athena. 
<img width="851" alt="er" src="https://user-images.githubusercontent.com/70564580/179433019-c55933fc-b0ba-4eda-ba09-afc64acc15d7.png">

### 2. Design a query to join orders table and order_products table together, filter on eval_set = ‘prior’
```sql
select * from 
(select * from orders where eval_set = 'prior') as t1
left join order_products as t2
on t1.order_id = t2.order_id;
```

## Part2 Athena queries
### 1. Create a table called order_products_prior by using the last SQL query you created from the previous assignment. It should be similar to below (note you need to replace the s3 bucket name “imba” to yours own bucket name):
```sql
CREATE TABLE order_products_prior WITH (external_location = 's3://imba-ming3/features/order_products_prior/', format = 'parquet')
as (SELECT a.*, b.product_id,
b.add_to_cart_order,
b.reordered FROM orders a
JOIN order_products b
ON a.order_id = b.order_id
WHERE a.eval_set = 'prior');
```

### 2. Create a SQL query (user_features_1). Based on table orders, for each user, calculate the max order_number, the sum of days_since_prior_order and the average of days_since_prior_order.
```sql
select user_id, max(order_number) as max_order_number,
sum(days_since_prior_order ) as sum_days_since_prior_order,
round(avg(days_since_prior_order)) as average_days_since_prior_order
from "prd"."orders"
group by user_id;
```

### 3. Create a SQL query (user_features_2). Similar to above, based on table order_products_prior, for each user calculate the total number of products, total number of distinct products, and user reorder ratio(number of reordered = 1 divided by number of order_number > 1)
```sql
select user_id,sum(order_number) as total_product_number,
count(distinct(product_id)) as total_discproduct_number,
(cast(sum(reordered) as double) / cast(sum(order_number) as double)) as reordered_ratio
from order_products_prior
where reordered = 1
and order_number > 1
group by user_id;
```

### 4. Create a SQL query (up_features). Based on table order_products_prior, for each user and product, calculate the total number of orders, minimum order_number, maximum order_number and average add_to_cart_order.
```sql
select user_id, product_id,
count(distinct order_id) as total_order_num,
max(order_number) as max_order_num, min(order_number) as min_order_num, 
round(avg(add_to_cart_order),2) as avg_addtocart_order_num
from order_products_prior
group by user_id, product_id;
```

### 5. Create a SQL query (prd_features). Based on table order_products_prior, first write a sql query to calculate the sequence of product purchase for each user, and name it product_seq_time (For example, if a user first time purchase a product A, mark it as 1. If it’s the second time a user purchases a product A, mark it as 2). Below are some examples:

# jiangren_project
### 1. Draw an ER model for the 5 five tables created in Athena. 
photo here
### 2. Design a query to join orders table and order_products table together, filter on eval_set = ‘prior’
```
select * from 
(select * from orders where eval_set = 'prior') as t1
left join order_products as t2
on t1.order_id = t2.order_id;
```

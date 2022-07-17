# innodata_project
## innodata_project
### 1. Draw an ER model for the 5 five tables created in Athena. 
<img width="901" alt="Screen Shot 2022-06-28 at 8 41 10 pm" src="https://user-images.githubusercontent.com/70564580/176159860-80bcd447-4fcd-4db0-b18f-666f20ca5442.png">

### 2. Design a query to join orders table and order_products table together, filter on eval_set = ‘prior’
```
select * from 
(select * from orders where eval_set = 'prior') as t1
left join order_products as t2
on t1.order_id = t2.order_id;
```

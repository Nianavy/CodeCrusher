https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL112`

```sql
select s.store_id,store_name ,product_category ,sum(inventory_quantity) inventory_quantity, sum(sales_amount) sales_amount
from stores s
join sales_inventory s2
on s2.store_id =s.store_id 
join products p
on p.product_id=s2.product_id
group by s.store_id,store_name,product_category ,p.product_id
having sum(inventory_quantity)<10 and sum(sales_amount)>5000
order by s.store_id,p.product_id;
```

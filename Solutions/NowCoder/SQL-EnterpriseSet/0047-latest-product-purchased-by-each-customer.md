https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL87`

```sql
select o.customer_id, c.customer_name,
latest_order
from(
    select 
    o.customer_id, p.product_name as latest_order,
    row_number() over(partition by o.customer_id order by o.order_date desc) as rk
    from orders o
    join products p on o.product_id = p.product_id
)o
join customers c on c.customer_id = o.customer_id
where rk = 1;
```

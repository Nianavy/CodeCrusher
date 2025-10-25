https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL82`

```sql
select o.order_id, c.customer_name, o.order_date
from orders o
join customers c on o.customer_id = c.customer_id
where (o.customer_id, o.order_id) in (
    select customer_id, max(order_id)
    from orders
    group by customer_id
)
order by c.customer_name asc;
```

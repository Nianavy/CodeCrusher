https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL84`

```sql
select o.order_id, c.customer_name, o.order_date
from (
    select order_id, order_date, customer_id,
           row_number() over(partition by customer_id order by order_date desc) as rk
    from orders
) o
join customers c on c.customer_id = o.customer_id
where o.rk = 1;
```

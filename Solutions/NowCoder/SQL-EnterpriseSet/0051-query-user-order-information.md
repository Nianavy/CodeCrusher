https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL92`

```sql
select c.city,
sum(o.total_amount) as total_order_amount
from orders o
join customers c on c.customer_id = o.customer_id
group by c.city
order by total_order_amount desc, c.city asc;
```

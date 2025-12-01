https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL119`

```sql
select
    c.name customer_name,
    sum(price) total_travel_cost,
    count(customer_id) order_count,
    round(avg(p.price), 2) avg_order_price
from
    bookings b
    join packages p on b.package_id = p.id
    join customers c on c.id = b.customer_id
    where booking_date BETWEEN '2024-01-01' and '2024-12-31'
group by
    c.name
having
    total_travel_cost > 10000
order by
    total_travel_cost desc;
```

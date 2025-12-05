https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL124`

```sql
select
    c.customer_id,
    c.customer_name,
    count(distinct o.order_id) as feb_2024_order_count,
    round(ifnull(sum(qty*price),0),2)feb_2024_total_amount,
    round(ifnull(sum(qty*price)/count(distinct o.order_id),0),2)feb_2024_avg_order_amount,
    min(order_date) as feb_2024_first_order_date,
    max(order_date) as feb_2024_last_order_date
from
    customers as c
    left join orders as o on c.customer_id = o.customer_id and order_date like '%2024-02%'
    left join order_items as oi on o.order_id = oi.order_id
group by
    c.customer_id,
    c.customer_name
order by
    feb_2024_total_amount desc,
    c.customer_id asc;
```

https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL130`

```sql
select
    category,
    orders_cnt,
    buyers_cnt,
    items_qty,
    revenue,
    ifnull(round(revenue/orders_cnt,2),0) avg_order_value,
    rank() over(order by revenue desc,orders_cnt desc,category) rank_by_revenue
from( 
    select
        category,
        count(distinct o.order_id) orders_cnt,
        count(distinct o.buyer_id) buyers_cnt,
        sum(i.qty) items_qty,
        sum(p.price*i.qty) revenue
    from product p
    left join order_items i using(product_id)
    left join orders o using(order_id)
    where left(order_date,7)='2024-08'
    group by category) tmp
order by 
    revenue desc,orders_cnt desc,category;
```

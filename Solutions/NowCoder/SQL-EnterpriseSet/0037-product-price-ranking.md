https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL76`

```sql
select
    p.product_id,
    p.product_name,
    p.type,
    p.price
from(
    select
        price,
        row_number() over(partition by type order by price desc) as rk
    from
        product_info
    group by
        type, price
) sub
join product_info p on p.price = sub.price 
where
    sub.rk <= 2
order by
    p.price desc, p.product_name
limit 3;
```

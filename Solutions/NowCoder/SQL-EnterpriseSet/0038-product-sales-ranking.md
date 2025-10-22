https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL77`

```sql
select 
    p.product_name,
    cast(sum(p.price) as signed) as price
from
    user_client_log u
    join product_info p on p.product_id = u.product_id
where
    u.step = 'select' and pay_method is not null
group by
    p.product_name
order by
    price desc
limit 2;
```

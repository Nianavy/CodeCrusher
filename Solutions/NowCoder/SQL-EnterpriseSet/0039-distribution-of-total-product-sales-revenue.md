https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL78`

```sql
select 
    case when u.pay_method = '' then 'error' else u.pay_method end as pay_method,
    count(*) as num
from
    user_client_log u
    join product_info p on u.product_id = p.product_id
where
    p.product_name = 'anta' and step = 'select'
group by 
    u.pay_method
order by
    num desc;
```

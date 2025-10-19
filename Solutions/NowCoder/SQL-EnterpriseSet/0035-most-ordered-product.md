https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL74`

```sql
select
    product_id,
    count(*) as cnt
from
    user_client_log
where
    step = 'order'
group by
    product_id
order by
    cnt desc, product_id
limit 1;
```

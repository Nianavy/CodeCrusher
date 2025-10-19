https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL75`

```sql
select
    uid,
    count(*) as cnt
from
    user_client_log
where 
    step = 'order'
group by
    uid
order by
    cnt desc, uid
limit 3;
```

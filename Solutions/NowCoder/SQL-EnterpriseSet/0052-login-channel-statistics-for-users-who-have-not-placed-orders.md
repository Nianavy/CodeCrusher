https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL93`

```sql
select channel,
count(*) as cnt
from user_info
where uid not in (select uid from order_log)
group by channel
order by cnt desc, channel
limit 1;
```

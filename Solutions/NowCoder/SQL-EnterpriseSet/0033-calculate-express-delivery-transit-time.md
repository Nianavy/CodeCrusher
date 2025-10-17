https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL72`

```sql
select
    e.exp_type,
    round(avg(timestampdiff(minute, out_time, in_time) / 60), 1) as time
from
    express_tb e
    join exp_action_tb a on e.exp_number = a.exp_number
group by
    e.exp_type
order by
    time asc;
```

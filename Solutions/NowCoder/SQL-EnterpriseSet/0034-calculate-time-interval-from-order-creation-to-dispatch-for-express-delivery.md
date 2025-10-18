https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL73`

```sql
select
    round(avg(timestampdiff(minute, e.create_time, a.out_time) / 60), 3) as time
from
    express_tb e
    join exp_action_tb a on e.exp_number = a.exp_number;
```

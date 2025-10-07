https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL62`

```sql
select
    cast(avg(abs(timestampdiff(second, o.logtime, s.logtime))) as signed) as gap
from
    order_log o
join 
    select_log s on s.order_id = o.order_id;
```

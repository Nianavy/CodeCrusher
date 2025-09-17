https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL43`

```sql
select
    date_format(t_time, '%Y-%m') as 'time',
    round(sum(t_amount), 1) as total
from
    trade t
    join customer c on t.t_cus = c.c_id
where
    year(t_time) = '2023'
    and t.t_type = 1
    and c.c_name = 'Tom'
group by
    date_format(t_time, '%Y-%m')
order by
    date_format(t_time, '%Y-%m');
```

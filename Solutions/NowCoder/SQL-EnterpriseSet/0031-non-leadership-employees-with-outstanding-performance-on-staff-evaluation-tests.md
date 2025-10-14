https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL70`

```sql
with avg_every as(
    select
        avg(timestampdiff(second, re.start_time,re.submit_time)) as time_avg,
        avg(re.score) as score_avg,
        re.exam_id
    from
        emp_info inf
    join
        exam_record re on re.emp_id = inf.emp_id
    group by
        re.exam_id
)
,exam_target as(
    select
        r.emp_id,
        r.exam_id
    from
        avg_every a
        join exam_record r on r.exam_id = a.exam_id
    where
        r.score > a.score_avg and timestampdiff(second, r.start_time,r.submit_time) < a.time_avg
)
select
    t.emp_id,
    i.emp_level,
    e.tag
from
    exam_target t
join
    examination_info e on t.exam_id = e.exam_id
join
    emp_info i on i.emp_id = t.emp_id
where
    i.emp_level < 7
order by
    i.emp_id asc,e.exam_id asc;
```

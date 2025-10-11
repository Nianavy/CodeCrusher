https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL67`

```sql
select
    sub.cid,
    sub.pv,
    sub.rk
from
    (
        select
            p.cid,
            round(count(*) * 1.0, 3) as pv,
            row_number () over (
                order by
                    count(*) desc,
                    c.release_date desc
            ) as rk
        from
            course_info_tb c
            join play_record_tb p on c.cid = p.cid
        group by
            p.cid,
            p.uid,
            c.release_date
    ) sub
where
    sub.pv > 1 and sub.rk <=3
order by
    rk asc;
```

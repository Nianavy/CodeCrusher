https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL88`

```sql
select
    cid,
    round(cast(max(cnt) as float), 3) as max_peak_uv
from (
    select
        a.cid,
        sum(if(a.start_time between b.start_time and b.end_time, 1, 0)) as cnt
    from play_record_tb a
    join play_record_tb b on a.cid=b.cid
    group by a.id, a.cid, a.start_time
) t
group by cid
order by max_peak_uv desc
limit 3;
```

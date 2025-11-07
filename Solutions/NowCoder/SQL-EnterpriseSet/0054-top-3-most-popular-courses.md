https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL95`

```sql
select p.cid,
count(*) as pv,
sum(timestampdiff(minute, p.start_time, p.end_time)) as time_len
from course_info_tb c 
join play_record_tb p on p.cid = c.cid
where 
    timestampdiff(day, c.release_date, date_format(p.start_time, '%Y-%m-%d')) <= 7
group by p.cid
having avg(p.score) >= 3
order by time_len desc
limit 3;
```

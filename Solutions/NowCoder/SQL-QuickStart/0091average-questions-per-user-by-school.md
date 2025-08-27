https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL25`

```SQL
select 
    u.university,
    round(count(q.question_id) * 1.0 / count(distinct q.device_id), 4) as avg_answer_cnt
from 
    user_profile u
inner join 
    question_practice_detail q
on 
    u.device_id = q.device_id
group by 
    u.university
order by 
    u.university;
```

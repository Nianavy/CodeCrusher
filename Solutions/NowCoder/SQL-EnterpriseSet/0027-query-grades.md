https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL66`

```sql
select
    count(*)
from(
    select
    Student.sId
    from
    SC
    join Student on Student.sId = SC.sId
    join Course on  Course.cId = SC.cId
    where 
    Course.cname = '语文' or Course.cname = '数学' or Course.cname = '英语'
    group by
    Student.sId
    having 
    avg(SC.score) > 60
)s;
```

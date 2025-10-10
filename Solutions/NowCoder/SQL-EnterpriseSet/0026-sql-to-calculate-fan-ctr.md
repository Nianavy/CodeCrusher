https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL65`

```sql
select
    sum(c.read_num) / sum(c.show_num) as fans_ctr
from
    a
    join b on a.author_id = b.author_id
    join c on b.content_id = c.content_id
where
    a.fans_id = c.fans_id;
```

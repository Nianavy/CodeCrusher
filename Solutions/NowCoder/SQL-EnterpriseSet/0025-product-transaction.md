https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL64`

```sql
select
    t.goods_id as id,
    g.name,
    g.weight,
    sum(count) as total
from
    goods g
join
    trans t on g.id = t.goods_id
group by
    t.goods_id, g.name, g.weight
having 
    sum(count) > 20 and g.weight < 50
order by
    t.goods_id asc;
```

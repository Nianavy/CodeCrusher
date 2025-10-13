https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL69`

```sql
select
    round(sum(s.sales_num * g.goods_price), 3) as sales_total,
    round(sum(s.sales_num * g.goods_price) / count(distinct s.user_id), 3) as per_trans
from
    sales_tb s
join 
    goods_tb g on s.goods_id = g.goods_id;
```

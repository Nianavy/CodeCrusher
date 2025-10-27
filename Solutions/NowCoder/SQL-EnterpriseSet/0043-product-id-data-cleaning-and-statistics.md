https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL83`

```sql
select 
    #regexp_substr(order_id,'p[0-9]+$') as product_id,#(方法一)
    substring_index(order_id,'_',-1) as product_id,#（方法二）
    count(*) as cnt
from order_log
group by product_id
order by cnt desc
limit 1;
```

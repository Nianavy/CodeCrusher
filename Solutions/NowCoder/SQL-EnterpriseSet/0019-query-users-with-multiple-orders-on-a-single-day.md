https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL58`

```sql
SELECT 
    DATE(ot.order_time) AS order_date,
    ot.user_id,
    COUNT(ot.user_id) AS order_nums
FROM 
    order_tb ot
GROUP BY 
    DATE(ot.order_time), ot.user_id
HAVING 
    COUNT(ot.user_id) > 1
ORDER BY 
    order_date ASC,
    order_nums DESC;
```

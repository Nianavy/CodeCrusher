https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL58`

```sql
SELECT
    DATE(order_time) AS order_date,
    o.user_id,
    COUNT(order_time) AS order_nums,
    vip
FROM order_tb o
JOIN uservip_tb u
ON o.user_id = u.user_id
GROUP BY order_date, user_id, vip
HAVING order_nums > 1;
```

https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL54`

```sql
WITH daily_visits AS (
    SELECT DATE(visit_time) AS date, COUNT(DISTINCT user_id) AS visit_users
    FROM visit_tb
    GROUP BY DATE(visit_time)
),
daily_orders AS (
    SELECT DATE(order_time) AS date, COUNT(DISTINCT user_id) AS order_users
    FROM order_tb
    GROUP BY DATE(order_time)
)
SELECT 
    v.date,
    CONCAT(ROUND(COALESCE(o.order_users, 0) / v.visit_users * 100, 1), '%') AS cr
FROM 
    daily_visits v
LEFT JOIN 
    daily_orders o ON v.date = o.date
ORDER BY 
    v.date;
```

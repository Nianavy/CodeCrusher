https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL61`

```sql
WITH earliest_logins AS (
    SELECT 
        DATE(log_time) AS log_day,
        MIN(log_time) AS earliest_time
    FROM 
        login_tb
    GROUP BY 
        DATE(log_time)
)

SELECT 
    e.log_day,
    u.user_id,
    u.hobby
FROM 
    earliest_logins e
JOIN 
    login_tb l ON l.log_time = e.earliest_time
JOIN
    user_action_tb u ON u.user_id = l.user_id
ORDER BY 
    e.log_day;
```

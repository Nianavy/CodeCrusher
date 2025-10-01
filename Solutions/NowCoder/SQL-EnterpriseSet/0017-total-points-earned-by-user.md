https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL56`

```sql
SELECT 
    user_id,
    SUM(FLOOR(TIMESTAMPDIFF(MINUTE, visit_time, leave_time)/10)) AS point
FROM 
    visit_tb
GROUP BY 
    user_id
ORDER BY 
    point DESC;
```

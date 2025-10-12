https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL68`

```sql
SELECT 
    u.room_id,
    r.room_name,
    COUNT(DISTINCT u.user_id) AS user_count
FROM 
    user_view_tb u
JOIN 
    room_info_tb r ON u.room_id = r.room_id
WHERE 
     u.in_time <= '23:59:59' AND u.out_time >= '23:00:00'
GROUP BY 
    u.room_id, r.room_name
ORDER BY 
    user_count DESC;
```

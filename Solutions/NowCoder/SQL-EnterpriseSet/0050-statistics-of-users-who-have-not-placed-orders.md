https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL90`

```sql
SELECT COUNT(*) AS cnt
FROM user_info
WHERE uid NOT IN (SELECT uid FROM order_log);
```

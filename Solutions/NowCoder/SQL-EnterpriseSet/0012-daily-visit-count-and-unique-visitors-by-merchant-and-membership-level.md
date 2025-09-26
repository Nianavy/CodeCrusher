https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL51`

```sql
SELECT
    u.vip,
    COUNT(v.info_id) AS visit_nums,
    COUNT(DISTINCT v.user_id) AS visit_users
FROM
    visit_tb v
JOIN
    uservip_tb u ON v.user_id = u.user_id
GROUP BY
    u.vip
ORDER BY
    visit_nums DESC;
```

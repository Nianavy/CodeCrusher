https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL49`

```sql
SELECT
    s.post,
    ROUND(AVG(TIMESTAMPDIFF(MINUTE, a.first_clockin, a.last_clockin) / 60), 3) AS work_hours
FROM
    staff_tb s
JOIN
    attendent_tb a ON s.staff_id = a.staff_id
WHERE
    a.first_clockin IS NOT NULL
    AND a.last_clockin IS NOT NULL
GROUP BY
    s.post
ORDER BY
    work_hours DESC;
```

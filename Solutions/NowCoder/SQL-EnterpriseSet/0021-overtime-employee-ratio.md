https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL60`

```sql
WITH work_hours AS (
    SELECT 
        s.department,
        a.staff_id,
        TIMESTAMPDIFF(MINUTE, a.first_clockin, a.last_clockin) / 60.0 AS hours_worked
    FROM 
        staff_tb s
    JOIN 
        attendent_tb a ON s.staff_id = a.staff_id
),
overtime_counts AS (
    SELECT 
        department,
        COUNT(CASE WHEN hours_worked > 9.5 THEN 1 END) AS overtime_count,
        COUNT(*) AS total_count
    FROM 
        work_hours
    GROUP BY 
        department
)
SELECT 
    department,
    CONCAT(ROUND(overtime_count / total_count * 100, 1), '%') AS ratio
FROM 
    overtime_counts
ORDER BY 
    ratio DESC;
```

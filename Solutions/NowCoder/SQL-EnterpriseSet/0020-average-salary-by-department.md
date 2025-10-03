https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL59`

```sql
SELECT 
    s.department,
    ROUND(AVG(sa.normal_salary - sa.dock_salary), 3) AS avg_salary
FROM 
    staff_tb s
JOIN 
    salary_tb sa ON s.staff_id = sa.staff_id
WHERE 
    (sa.normal_salary - sa.dock_salary) BETWEEN 4000 AND 30000
GROUP BY 
    s.department
ORDER BY 
    avg_salary DESC;
```

https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL101`

```sql
SELECT
    a.employee_id,
    b.employee_name,
    a.performance_score as first_half_2024_score,
    ROW_NUMBER() OVER (PARTITION BY c.project_name ORDER BY a.performance_score DESC,a.employee_id ASC) AS project_group_rank,
    b.department,
    c.project_name as project_group
FROM
    performance as a 
    INNER JOIN employees as b ON a.employee_id = b.employee_id
    INNER JOIN projects as c ON a.project_id = c.project_id
WHERE
    c.start_date BETWEEN '2024-01-01' AND '2024-06-30'
ORDER BY c.project_id ASC,project_group_rank ASC;
```

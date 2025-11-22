https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL110`

```sql
select employee_id,department_name,performance_score
from(
    select a.employee_id,department_name,performance_score,
            avg(performance_score)over(partition by department_name) as avg_score
    from employee_projects as a
    inner join department_info as b
    on a.employee_id = b.employee_id
) as temp1
where performance_score >= avg_score
order by employee_id;
```

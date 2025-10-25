https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL80`

```sql
select department, employee_name, salary
from (
    select department, employee_name, salary,
           rank() over(partition by department order by salary desc) as rk
    from employees
) sub
where rk <= 2
order by department asc, salary desc;
```

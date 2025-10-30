https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL86`

```sql
select sf.department,
round(avg(sy.normal_salary - sy.dock_salary), 2) as average_actual_salary,
ifnull(round(avg(if(sf.staff_gender = 'male', sy.normal_salary - sy.dock_salary, null)), 2), 0.00) as average_actual_salary_male,
ifnull(round(avg(if(sf.staff_gender = 'female', sy.normal_salary - sy.dock_salary, null)), 2), 0.00) as average_actual_salary_female
from staff_tb sf
join salary_tb sy on sf.staff_id = sy.staff_id
group by department
order by average_actual_salary desc;
```

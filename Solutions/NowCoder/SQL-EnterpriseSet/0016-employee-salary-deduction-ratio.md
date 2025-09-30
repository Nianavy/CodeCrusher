https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL55`

```sql
select 
    st.staff_id,
    st.staff_name,
    concat(round((tb.dock_salary/tb.normal_salary)*100, 1),'%') as dock_ratio
from 
    staff_tb st
join 
    salary_tb tb on st.staff_id = tb.staff_id
where
    st.department = 'dep1'
order by
    dock_ratio desc;
```

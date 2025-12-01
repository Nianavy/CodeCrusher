https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL120`

```sql
select s1.city,s1.total_loan_amount,s1.average_loan_amount,s1.total_customers,
s2.loan_type_name as most_applied_loan_type
from (
    select c.city,
    round(sum(la.loan_amount),2) as total_loan_amount,
    round(sum(la.loan_amount)/count(distinct c.customer_id),2) as average_loan_amount,
    count(distinct c.customer_id) as total_customers
    from loan_applications la
    join customers c on c.customer_id = la.customer_id
    join loan_application_types lat on lat.application_id = la.application_id
    join loan_types lt on lat.loan_type_id = lt.loan_type_id
    group by c.city
)s1
join (
        select c.city,lt.loan_type_id,lt.loan_type_name,
        rank() over(partition by c.city order by count(lat.loan_type_id) desc,lat.loan_type_id asc) as rk
        from loan_applications la
        join customers c on c.customer_id = la.customer_id
        join loan_application_types lat on lat.application_id = la.application_id
        join loan_types lt on lat.loan_type_id = lt.loan_type_id
        group by c.city,lt.loan_type_id,lt.loan_type_name
    )s2 on s2.city = s1.city
where rk = 1;
```

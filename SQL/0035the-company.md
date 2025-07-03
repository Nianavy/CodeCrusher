https://www.hackerrank.com/challenges/the-company

```SQL
select c.company_code,
       c.founder,
       count(distinct lead_manager_code),
       count(distinct senior_manager_code),
       count(distinct manager_code),
       count(distinct employee_code)
from Company c
     join Employee using (company_code)
group by c.company_code, c.founder
order by c.company_code;
```

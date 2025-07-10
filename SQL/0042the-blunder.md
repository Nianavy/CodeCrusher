https://www.hackerrank.com/challenges/the-blunder

```SQL
select ceil(avg(org_sal) - avg(mod_sal))
from 
    (select 
        salary org_sal,
        cast(replace(cast(salary as char), '0', '') as unsigned) as mod_sal
    from employees) as mod_tbl;
```

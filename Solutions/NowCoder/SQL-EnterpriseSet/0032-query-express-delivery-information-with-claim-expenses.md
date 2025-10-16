https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL71`

```sql
select
    e.exp_number,
    e.exp_type,
    c.claims_cost
from
    express_tb e
    join exp_cost_tb c on e.exp_number = c.exp_number
where
    c.claims_cost is not null
order by
    c.claims_cost desc;
```

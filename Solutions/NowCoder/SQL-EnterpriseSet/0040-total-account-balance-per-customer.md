https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL79`

```sql
select
    customer_id,
    sum(balance) as sum_balance
from
    account
group by
    customer_id
order by
    sum_balance desc,
    customer_id asc;
```

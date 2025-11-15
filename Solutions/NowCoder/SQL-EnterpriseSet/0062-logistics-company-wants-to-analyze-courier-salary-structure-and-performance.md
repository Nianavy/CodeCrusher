https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL103`

```sql
with t1 as (
    select courier_id,
           sum(delivery_fee) as 'total_delivery_fee'
    from deliveries_info
    where delivery_date between '2024-07-01' and '2024-07-31'
    group by courier_id
),
t2 as (
    select courier_id,
        sum(expense_amount) as 'total_expense_amount'
    from expenses_info
    where expense_date between '2024-07-01' and '2024-07-31'
    group by courier_id
)
select ci.courier_id,
       ci.courier_name,
       ci.base_salary + t1.total_delivery_fee - t2.total_expense_amount as 'total_income'
from couriers_info ci
         inner join t1 on ci.courier_id = t1.courier_id
         inner join t2 on ci.courier_id = t2.courier_id;
```

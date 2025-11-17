https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL105`

```sql
select
    industry,
    sum(sale_amount) total_sales_amount
from
    merchants_underline m join sales_underline s on m.merchant_id = s.merchant_id
group by
    industry
order by
    sum(sale_amount) desc,industry;
```

https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL96`

```sql
select
    category as product_category,
    age_group,
    sum(quantity * price) as total_sales_amount,
  round(sum(quantity * price)/sum(sum(quantity * price)) over(partition by category ) ,2)  as purchase_percentage
from
    sales
    join products using (product_id)
    join customer_info using (sale_id)
group by
    category,age_group
    order by category asc,purchase_percentage desc;
```

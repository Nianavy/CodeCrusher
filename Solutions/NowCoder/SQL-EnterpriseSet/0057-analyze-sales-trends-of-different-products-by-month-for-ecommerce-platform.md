https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL98`

```sql
select
    product_id,
    product_name,
    round(sum(sales),0) as total_sales,
    round(max(sales),0) as max_monthly_sales,
    round(min(sales),0) as min_monthly_sales,
    round(avg(sales),0) as avg_monthly_sales
from 
    (select 
        p.product_id,
        p.product_name,
        s.sale_month,
        sum(s.quantity) as sales
    from
        products_underline p 
        join sales_underline s on p.product_id=s.product_id
    where
        sale_month between '2024-01' and '2024-06'
    group by
        p.product_id,
        p.product_name,
        s.sale_month) sub
group by
    product_id,
    product_name
order by
    product_id asc;
```

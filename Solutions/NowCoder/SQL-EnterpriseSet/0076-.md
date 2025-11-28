https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL117`

```sql
select product_id,product_name,competitor_name,
total_sales_amount_2023 as total_sales_amount_of_product,
(total_sales_amount_2023-total_competitor_sales_amount_2023) as sales_difference_with_competitor
from oppo_products_detail
left join 
(
select product_id,
sum(quarter_1_sales_amount+quarter_2_sales_amount+quarter_3_sales_amount+quarter_4_sales_amount) as total_sales_amount_2023
from sales_info
group by product_id
)t1
using(product_id)
join competitor_analysis t2
using(product_id);
```

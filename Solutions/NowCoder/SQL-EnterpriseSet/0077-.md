https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL118`

```sql
select a.product_line,b.region,b.channel_name,
sum(c.sale_amount) as total_sale_amount,
count(*) as total_sale_quantity
from oppo_products a join sales_data c using(product_id)
join sales_channels b using(channel_id)
group by a.product_line,b.region,b.channel_name,b.channel_id
order by a.product_line,b.channel_id;
```

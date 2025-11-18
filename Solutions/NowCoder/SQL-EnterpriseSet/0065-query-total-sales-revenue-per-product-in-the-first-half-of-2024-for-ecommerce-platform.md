https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL106`

```sql
select p.product_id, p.product_name,
sum(p.price * s.quantity) as total_sales
from products_underline p
join sales_underline s on p.product_id = s.product_id
where s.sale_month between '2024-01' and '2024-06'
group by product_id
order by product_id;
```

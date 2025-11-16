https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL104`

```sql
select category_id,
       sum(order_amount) as 'total_sales',
       count(if(customer_gender = '男',1,null)) as 'male_customers',
       count(if(customer_gender = '女',1,null)) as 'female_customers'
from order_details od
         inner join customer_info ci on od.order_id = ci.order_id
where order_date between '2024-01-01' and '2024-06-30'
group by category_id;
```

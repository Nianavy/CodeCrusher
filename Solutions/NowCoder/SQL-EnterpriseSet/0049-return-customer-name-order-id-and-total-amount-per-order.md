https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL89`

```sql
select c.cust_name,os.order_num,sum(os.quantity*os.item_price) OrderTotal from Orders o
join OrderItems os
on os.order_num=o.order_num
join Customers c
on c.cust_id=o.cust_id
group by c.cust_name,os.order_num
order by c.cust_name,os.order_num;
```

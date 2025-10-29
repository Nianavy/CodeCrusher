https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL85`

```sql
with t1 as
( # 把主要数据进行查询
    select
        p.product_id,
        sum(quantity * unit_price) as total_sales,
        unit_price,
        sum(quantity) as total_quantity,
        sum(quantity * unit_price) / 12 as avg_monthly_sales
    from products as p
    inner join orders as s on p.product_id = s.product_id
    group by p.product_id
),
t2 as
(   #以 产品 月份 进行统计数量
    select
        p.product_id,
        month(order_date) as m,
        sum(quantity) as ms
    from products as p
    inner join orders as s on p.product_id = s.product_id
    group by p.product_id,m
),
t3 as
(   # 找到数量最大的月份
    select
        product_id,
        m,
        max(ms) as max_monthly_quantity
    from t2 
    group by product_id,m
),
t4 as
(   #年龄分类，统计年龄数量
    select
        orders.product_id,
        case when customer_age between 1 and 10 then '1-10'
                       when customer_age between 11 and 20 then '11-20'
                       when customer_age between 21 and 30 then '21-30'
                       when customer_age between 31 and 40 then '31-40'
                       when customer_age between 41 and 50 then '41-50'
                       when customer_age between 51 and 60 then '51-60'
                    else '61+' end as customer_age_group,
        sum(quantity) as s
    from customers
    left join orders on customers.customer_id = orders.customer_id
    group by orders.product_id,customer_age_group
),
t5 as ( #年龄排序
    select
        product_id,
        customer_age_group,
        s,
        rank() over (partition by product_id order by s desc, customer_age_group asc) as rnk
    from t4
)
select
    distinct t1.product_id,
    total_sales,
    unit_price,
    total_quantity,
    round(avg_monthly_sales,2) as avg_monthly_sales,
    max(max_monthly_quantity) as max_monthly_quantity,
    customer_age_group
from t1
left join t3 on t1.product_id = t3.product_id
left join t5 on t1.product_id = t5.product_id
where rnk = 1
group by t1.product_id,total_sales,unit_price,total_quantity,avg_monthly_sales,customer_age_group
order by total_sales desc,t1.product_id ASC;
```

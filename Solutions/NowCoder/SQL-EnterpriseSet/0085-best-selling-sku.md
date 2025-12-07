https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL126`

```sql
-- 为了可读性强，本题分3步执行。
-- 第一步，3表关联，计算前7列。
with t1 as 
(select store_id,any_value(store_name) as store_name,any_value(city) as city,sku_id,sum(if(qty,qty,0)) as last7d_qty,round(sum(if(qty,qty,0))/7,2) as avg_daily_qty,any_value(stock_qty) as stock_qty
# 使用any_value()，避免group by分组，select中的非聚合列报错。
from store_info_ inner join store_stock_ using(store_id) 
left join sales_daily_ using(store_id,sku_id) 
/*题目要求“若某sku近7天无销量也要排名”，导致该题有一点小麻烦，store_info_ inner 与 store_stock_ 可以inner join，但与sales_daily_ 需要left join，特别需要注意，left join 完成后，没有销售记录的sku，它的销量qty、销售日期sale_date，都为null,所以下面进行日期过滤的时候需要加上“or sale_date is null”。*/
where sale_date between date_sub(snapshot_date,interval 6 day) and snapshot_date
or sale_date is null
group by store_id,sku_id),

-- 第二步，在第一步的基础上，计算后两列。
t2 as
(select store_id,store_name,city,sku_id,last7d_qty,avg_daily_qty,stock_qty,round(case when avg_daily_qty>0 then stock_qty/avg_daily_qty else null end,1) as coverage_days,row_number() over(partition by store_id order by last7d_qty desc,sku_id) as rank_in_store
from t1)

-- 第三步，输出所有列。
select store_id,store_name,city,sku_id, last7d_qty, avg_daily_qty,stock_qty, coverage_days,rank_in_store
from
t2
where rank_in_store <= 3;
```

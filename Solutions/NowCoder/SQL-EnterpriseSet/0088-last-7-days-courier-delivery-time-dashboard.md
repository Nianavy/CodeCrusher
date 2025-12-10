https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL129`

```sql
select
*
,rank()over(partition by city order by on_time_rate desc,avg_minutes_7d) rank_in_city
from (
    select
    parcel.courier_id 
    ,courier_name 
    ,city    
    ,count(1) orders_7d
    ,sum(case when promised_minutes >= timestampdiff(minute,shipped_ts,delivered_ts) then 1 else 0 end) on_time_7d
    ,ifnull(ROUND(sum(case when promised_minutes >= timestampdiff(minute,shipped_ts,delivered_ts) then 1 else 0 end) / count(1), 2),0) on_time_rate
    ,ROUND(AVG(TIMESTAMPDIFF(MINUTE, shipped_ts, delivered_ts)), 2) avg_minutes_7d
    from courier join parcel on parcel.courier_id = courier.courier_id 
    where date_format(delivered_ts,'%Y-%m-%d') between 
    date_sub((select
    date_format(delivered_ts,'%Y-%m-%d')
    from (
        select
        *
        ,row_number()over(order by delivered_ts desc) rk   
        from parcel
    ) t1
    where rk = 1),interval 7 day)
    and
    (select
    date_format(delivered_ts,'%Y-%m-%d')
    from (
        select
        *
        ,row_number()over(order by delivered_ts desc) rk   
        from parcel
    ) t1
    where rk = 1)    
    group by 1,2,3
) t2
where orders_7d > 0
order by 3,8,1;
```

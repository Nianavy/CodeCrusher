https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL122`

```sql
with t1 as (
select
    z.zone_id,
    z.zone_name,
    #  按日期和高峰期进行统计
    sum(case when substr(delivered_time, 1, 7) = '2023-02' and (HOUR(order_time) BETWEEN 11 AND 13 OR HOUR(order_time) BETWEEN 18 AND 20)  then 1  else 0 end ) as  peak_2023_02_delivered,
    sum(case when substr(delivered_time, 1, 7) = '2024-02'  and (HOUR(order_time) BETWEEN 11 AND 13 OR HOUR(order_time) BETWEEN 18 AND 20) then 1  else 0  end
    ) as peak_2024_02_delivered,
    sum(  case when substr(delivered_time, 1, 7) = '2024-01'  and (HOUR(order_time) BETWEEN 11 AND 13 OR HOUR(order_time) BETWEEN 18 AND 20)  then 1  else 0 end
    ) as peak_2024_01_delivered,
    # 算总的送达时间
  sum(case when substr(delivered_time, 1, 7) = '2024-02' and (HOUR(order_time) BETWEEN 11 AND 13 OR HOUR(order_time) BETWEEN 18 AND 20)  then timestampdiff(minute,order_time ,delivered_time)   else 0 end) as peak_minutes_2024_02
from
    Zones z
    join Orders o on z.zone_id = o.zone_id
    # join Couriers c on c.courier_id = o.courier_id
    where status    ='delivered'
group by
    z.zone_id,
    z.zone_name
),
t2 as (
    select
        z.zone_id,
        c.courier_id,
        c.courier_name ,
        # 商圈内排名 ,count(*)每个骑手在商圈送单数
        ROW_NUMBER() over(partition by z.zone_id order by count(*) desc , c.courier_id) as rn
    from 
    Zones z
    join Orders o on z.zone_id = o.zone_id
    join Couriers c on c.courier_id = o.courier_id
    where status    ='delivered'
    group by 
        z.zone_id,
        c.courier_id,
        c.courier_name  
)
select 
    t1.zone_id,
    t1.zone_name,
    peak_2023_02_delivered,
    peak_2024_02_delivered,
    peak_2024_01_delivered,
    peak_2024_02_delivered-peak_2023_02_delivered as yoy_delta,
    peak_2024_02_delivered-peak_2024_01_delivered as mom_delta,
    ROUND(peak_minutes_2024_02/peak_2024_02_delivered ,2) as avg_peak_minutes_2024_02,
    t2.courier_name as  top_courier_2024_02
 from t1 join t2 on t1.zone_id=t2.zone_id
 where t2.rn=1;
```

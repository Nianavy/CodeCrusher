https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL116`

```sql
select 
weather_type,
round(avg(delivery_time),2) as average_delivery_time,
count(*) as delivery_count
from (
select 	
record_id,
delivery_records.staff_id ,
weather_type ,
delivery_time from delivery_records  
left join weather_conditions  on delivery_records.weather_id  = weather_conditions.weather_id
left join delivery_staff on delivery_records.staff_id = delivery_staff.staff_id
where delivery_records.staff_id in (

select staff_id from (
    select 
delivery_staff.staff_id ,
avg(average_speed) as average_speed ,
sum(if(is_complaint=1,1,0)) as 投诉数量 ,
count(*) as 配送订单总数量
from delivery_records
left join delivery_staff  
on delivery_staff.staff_id = delivery_records.staff_id
group by delivery_staff.staff_id
)t1
where average_speed > 20 and 投诉数量/配送订单总数量 < 0.5))t2
group by weather_type;
```

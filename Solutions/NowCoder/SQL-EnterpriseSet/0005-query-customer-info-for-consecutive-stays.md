https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL44`

```sql
with t1 as(
    select user_id, room_id, datediff(checkout_time,checkin_time) as days
    from checkin_tb
)

select t1.user_id, t1.room_id , gt.room_type, t1.days
from t1 
join guestroom_tb gt
on t1.room_id=gt.room_id
where days>=2
order by days asc, room_id asc, user_id desc;
```

https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL127`

```sql
with logs as(
    select
        class_id
        ,count(distinct student_id) learners_active_m
        ,count(distinct case when finished_flag=1 then student_id else null end) finishers_m
        ,count(distinct case when finished_flag=1 then student_id else null end)/count(distinct student_id) completion_rate
        ,sum(watch_minutes) total_minutes_m
        ,sum(watch_minutes)/count(distinct student_id) avg_minutes_per_active
    from study_logs_
    where substring(log_ts,1,7)='2024-08'
    group by 1
)


,enroll as(
    select
        a.class_id
        ,course_id
        ,teacher_id
        ,count(student_id) learners_enrolled
    from course_class_ a
    left join course_enroll_ b on a.class_id=b.class_id
    group by 1,2,3
)

,combine as(
    select
        a.class_id,course_id,teacher_id
        ,learners_enrolled
        ,ifnull(learners_active_m,0) learners_active_m
        ,ifnull(finishers_m,0) finishers_m
        ,round(ifnull(completion_rate,0),2) completion_rate
        ,ifnull(total_minutes_m,0) total_minutes_m
        ,round(ifnull(avg_minutes_per_active,0),2) avg_minutes_per_active
        ,rank()over(partition by course_id order by ifnull(avg_minutes_per_active,0) desc) rank_in_course
    from enroll a
    left join logs b on a.class_id=b.class_id
)

select * from combine;
```

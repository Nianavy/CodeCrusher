https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL47`

```sql
SELECT
    ROUND(SUM(score) / COUNT(DISTINCT user_id), 3) AS avg_score
FROM
    user_action_tb
WHERE
    user_id
IN
    (
        SELECT user_id FROM user_action_tb AS tb1
        JOIN recommend_tb AS tb2
        ON tb1.user_id = tb2.rec_user AND tb1.hobby_l = tb2.rec_info_l
    );
```

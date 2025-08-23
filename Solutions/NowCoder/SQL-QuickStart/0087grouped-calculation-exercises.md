https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL21`

```SQL
SELECT
    gender,
    university,
    COUNT(*) AS user_num,
    AVG(active_days_within_30) AS avg_active_day,
    AVG(question_cnt) AS avg_question_cnt
FROM
    user_profile
GROUP BY
    university, gender
ORDER BY
    gender, university ASC;
```

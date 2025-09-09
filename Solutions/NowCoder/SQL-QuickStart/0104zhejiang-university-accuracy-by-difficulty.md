https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL38`

```SQL
SELECT
    difficult_level,
    AVG(if(qpd.result='right', 1, 0)) AS correct_rate
FROM
    user_profile AS up
INNER JOIN
    question_practice_detail AS qpd
ON
    up.device_id = qpd.device_id
INNER JOIN
    question_detail AS qd
ON
    qd.question_id = qpd.question_id
WHERE
    up.university = '浙江大学'
GROUP BY
    qd.difficult_level
ORDER BY
    correct_rate ASC;
```

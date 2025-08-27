https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL24`

```SQL
SELECT
    qpd.device_id,
    qpd.question_id,
    qpd.result
FROM
    question_practice_detail qpd
INNER JOIN
    user_profile up
ON
    qpd.device_id = up.device_id
WHERE
    up.university = '浙江大学'
ORDER BY
    qpd.question_id ASC;
```

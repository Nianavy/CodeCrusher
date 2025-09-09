https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL37`

```SQL
SELECT
    up.device_id,
    '复旦大学' AS university,
    COUNT(question_id) AS question_cnt,
    SUM(if(qpd.result = 'right', 1, 0)) AS right_question_cnt
FROM
    user_profile AS up
LEFT JOIN question_practice_detail AS qpd
ON qpd.device_id = up.device_id AND MONTH(qpd.date) = 8
WHERE up.university = '复旦大学'
GROUP BY up.device_id;
```

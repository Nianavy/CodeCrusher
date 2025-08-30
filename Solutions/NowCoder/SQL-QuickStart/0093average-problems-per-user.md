https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL27`

```SQL
SELECT 
    u.university,
    qd.difficult_level,
    ROUND(COUNT(qpd.question_id) * 1.0 / COUNT(DISTINCT u.device_id), 4) AS avg_answer_cnt
FROM 
    user_profile u
INNER JOIN 
    question_practice_detail qpd ON u.device_id = qpd.device_id
INNER JOIN 
    question_detail qd ON qpd.question_id = qd.question_id
WHERE 
    u.university = '山东大学'
GROUP BY 
    u.university, qd.difficult_level
ORDER BY 
    avg_answer_cnt DESC;
```

https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL26`

```SQL
SELECT 
    u.university,
    t.difficult_level,
    ROUND(AVG(t.answer_cnt), 4) AS avg_answer_cnt
FROM 
    user_profile u
INNER JOIN (
    SELECT 
        qpd.device_id,
        qd.difficult_level,
        COUNT(*) AS answer_cnt
    FROM 
        question_practice_detail qpd
    JOIN 
        question_detail qd 
    ON 
        qpd.question_id = qd.question_id
    GROUP BY 
        qpd.device_id, qd.difficult_level
) t 
ON 
    u.device_id = t.device_id
GROUP BY 
    u.university, t.difficult_level
ORDER BY 
    u.university, t.difficult_level;
```

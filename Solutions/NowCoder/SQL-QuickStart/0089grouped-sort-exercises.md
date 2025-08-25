https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL23`

```SQL
SELECT 
    university,
    AVG(question_cnt) AS avg_question_cnt
FROM user_profile
GROUP BY university
ORDER BY avg_question_cnt ASC;
```

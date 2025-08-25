https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL22`

```SQL
SELECT
    university,
    AVG(question_cnt) AS avg_question_cnt,
    AVG(answer_cnt) AS avg_answer_cnt
FROM user_profile
GROUP BY university
HAVING
    avg_question_cnt < 5
OR
    avg_answer_cnt < 20;
```

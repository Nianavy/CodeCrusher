https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL31`

```SQL
SELECT
    DAY(date) AS day, 
    COUNT(question_id) AS question_cnt 
FROM
    question_practice_detail 
WHERE
    MONTH(date)=8 AND YEAR(date)=2021
GROUP BY
    DAY(date);
```

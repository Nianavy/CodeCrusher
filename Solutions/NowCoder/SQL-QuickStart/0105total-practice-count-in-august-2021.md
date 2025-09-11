https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL39`

```SQL
SELECT
    COUNT(DISTINCT device_id) AS did_cnt,
    COUNT(question_id) AS question_cnt
FROM
    question_practice_detail
WHERE DATE LIKE "2021-08%";
```

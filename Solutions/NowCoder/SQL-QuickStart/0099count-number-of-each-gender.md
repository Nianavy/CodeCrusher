https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL33`

```SQL
SELECT
    SUBSTRING_INDEX(profile, ',', -1) AS gender,
    COUNT(*) AS number
FROM
    user_submit
GROUP BY
    gender;
```

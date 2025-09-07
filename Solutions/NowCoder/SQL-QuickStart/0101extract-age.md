https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL35`

```SQL
SELECT
    SUBSTRING_INDEX(SUBSTRING_INDEX(profile, ',', 3), ',', -1) AS age,
    COUNT(device_id) AS number
FROM user_submit
GROUP BY age;
```

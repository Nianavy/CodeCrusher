https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL36`

```SQL
SELECT
    device_id,
    university,
    gpa
FROM
    user_profile
WHERE
    (university, gpa) IN (
        SELECT
            university,
            MIN(gpa)
        FROM
            user_profile
        GROUP BY
            university
    )
ORDER BY
    university;
```

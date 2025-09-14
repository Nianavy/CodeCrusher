https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL42`

```SQL
SELECT
    *,
    abs(value) AS absolute_value,
    ceil(value) AS ceiling_value,
    floor(value) AS floor_value,
    round(value, 1) AS rounded_value
FROM
    numbers
ORDER BY id ASC;
```

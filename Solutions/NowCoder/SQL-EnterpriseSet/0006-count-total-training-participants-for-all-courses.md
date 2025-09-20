https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL45`

```sql
SELECT
    SUM(LENGTH(course) - LENGTH(REPLACE(course, ',', '')) + 1) AS staff_nums
FROM cultivate_tb;
```

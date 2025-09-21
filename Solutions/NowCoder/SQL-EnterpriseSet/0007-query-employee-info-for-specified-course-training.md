https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL46`

```sql
SELECT
    c.staff_id,
    s.staff_name
FROM
    cultivate_tb c
LEFT JOIN staff_tb s
ON c.staff_id = s.staff_id
WHERE c.course like '%course3%';
```

https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL17`

```SQL
SELECT device_id, gender, age, university, gpa
FROM user_profile
WHERE 
    (gpa > 3.5 AND university = '山东大学')
    OR
    (gpa > 3.8 AND university = '复旦大学')
ORDER BY device_id ASC;
```

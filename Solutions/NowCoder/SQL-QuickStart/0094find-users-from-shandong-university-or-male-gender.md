https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL28`

```SQL
SELECT device_id, gender, age, gpa
FROM user_profile
WHERE university = '山东大学'
UNION ALL
SELECT device_id, gender, age, gpa
FROM user_profile
WHERE gender = 'male';
```

https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL40`

```SQL
SELECT
    id,
    name,
    phone_number
FROM
    contacts
WHERE
    phone_number REGEXP '^[1-9][0-9]{2}-?[0-9]{3}-?[0-9]{4}$';
```

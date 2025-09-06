https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL34`

```SQL
SELECT 
    device_id, 
    SUBSTRING_INDEX(blog_url, '/', -1) AS user_name
FROM 
    user_submit;
```

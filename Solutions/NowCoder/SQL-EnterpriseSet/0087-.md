https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL128`

```sql
SELECT 
    a.author_id,
    a.author_name,
    COUNT(*) posts_30d,
    SUM(like_cnt) likes_30d,
    ROUND(IFNULL(SUM(like_cnt)/COUNT(*),0), 2) avg_likes_30d
FROM post p 
JOIN author a ON p.author_id = a.author_id
WHERE DATEDIFF((SELECT MAX(publish_ts)FROM post), publish_ts) BETWEEN 0 AND 29
GROUP BY a.author_id 
ORDER BY likes_30d DESC, posts_30d DESC, a.author_id
LIMIT 5;
```

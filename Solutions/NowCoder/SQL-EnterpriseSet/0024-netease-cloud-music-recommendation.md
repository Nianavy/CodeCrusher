https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL63`

```sql
SELECT music_name
FROM (
    SELECT DISTINCT m.id, m.music_name
    FROM music_likes ml
    JOIN follow f ON ml.user_id = f.follower_id
    JOIN music m ON ml.music_id = m.id
    WHERE f.user_id = 1
    AND ml.music_id NOT IN (
        SELECT music_id
        FROM music_likes
        WHERE user_id = 1
    )
) AS subquery
ORDER BY id;
```

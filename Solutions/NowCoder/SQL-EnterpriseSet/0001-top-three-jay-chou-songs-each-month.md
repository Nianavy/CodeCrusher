https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL40`

```sql
WITH t AS(
    SELECT
        MONTH(p.fdate) AS `month`,
        ROW_NUMBER() OVER (PARTITION BY MONTH(p.fdate)
        ORDER BY COUNT(s.song_name) DESC, p.song_id ASC)
        AS ranking,
        s.song_name,
        COUNT(s.song_name) AS play_pv
    FROM
        play_log p
        LEFT JOIN song_info s ON p.song_id = s.song_id
        LEFT JOIN user_info u ON p.user_id = u.user_id
    WHERE
        YEAR(p.fdate) = 2022
        AND u.age BETWEEN 18 AND 25
        AND s.singer_name = '周杰伦'
    GROUP BY MONTH(p.fdate), s.song_name, p.song_id
)
SELECT * FROM t WHERE ranking <= 3;
```

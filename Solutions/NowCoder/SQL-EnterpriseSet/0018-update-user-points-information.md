https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL57`

```sql
SELECT
    u.user_id,
    u.point + SUM(o.order_price) AS point
FROM
    uservip_tb u
    LEFT JOIN (
        SELECT
            user_id,
            order_price
        FROM
            order_tb
        WHERE
            order_price > 100
    ) o ON u.user_id = o.user_id
GROUP BY
    u.user_id
HAVING
    SUM(o.order_price) > 0
ORDER BY
    point DESC;
```

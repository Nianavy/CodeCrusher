https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL52`

```sql
SELECT
    u.vip,
    COALESCE(SUM(o.order_price), 0) AS order_total
FROM
    uservip_tb u
LEFT JOIN
    order_tb o ON u.user_id = o.user_id
GROUP BY
    u.vip
ORDER BY
    order_total DESC;
```

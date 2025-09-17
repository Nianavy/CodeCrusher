https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL42`

```sql
SELECT
    c.pay_ability,
    CONCAT(
        ROUND(
            100.0 * COUNT(
                DISTINCT CASE
                    WHEN l.overdue_days IS NOT NULL THEN l.customer_id
                END
            ) / COUNT(DISTINCT c.customer_id),
            1
        ),
        '%'
    ) AS overdue_ratio
FROM
    customer_tb c
    LEFT JOIN loan_tb l ON c.customer_id = l.customer_id
GROUP BY
    c.pay_ability ORDER BY overdue_ratio DESC;
```

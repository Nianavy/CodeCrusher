https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL102`

```sql
SELECT
    b.brand_id,
    b.brand_name,
    ROUND(SUM(s.return_status)/COUNT(*),2) AS return_rate_July_2024,
    ROUND(AVG(c.customer_satisfaction_score),2) AS average_customer_satisfaction_score
FROM
    brand_info AS b
    INNER JOIN
    sales_orders AS s ON b.brand_id=s.brand_id
        INNER JOIN
        customer_feedback AS c ON s.order_id=c.order_id
WHERE
    DATE_FORMAT(s.order_date,'%Y-%m')='2024-07'
GROUP BY
    b.brand_id,
    b.brand_name
ORDER BY
    b.brand_id;
```

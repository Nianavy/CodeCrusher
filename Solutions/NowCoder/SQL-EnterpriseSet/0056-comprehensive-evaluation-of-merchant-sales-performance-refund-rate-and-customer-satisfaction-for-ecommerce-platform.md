https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL97`

```sql
SELECT 
    a.merchant_id,
    a.merchant_name,
    COALESCE(b.total_sales, 0) AS total_sales_amount,
    COALESCE(c.total_refunds, 0) AS total_refund_amount,
    ROUND(COALESCE(d.avg_satisfaction, 0),2) AS average_satisfaction_score
FROM 
    merchants_underline a
LEFT JOIN (
    SELECT merchant_id, SUM(sale_amount) AS total_sales
    FROM sales_underline
    GROUP BY merchant_id
) b ON a.merchant_id = b.merchant_id
LEFT JOIN (
    SELECT merchant_id, SUM(refund_amount) AS total_refunds
    FROM refunds_underline
    GROUP BY merchant_id
) c ON a.merchant_id = c.merchant_id
LEFT JOIN (
    SELECT merchant_id, AVG(satisfaction_score) AS avg_satisfaction
    FROM satisfaction_underline
    GROUP BY merchant_id
) d ON a.merchant_id = d.merchant_id;
```

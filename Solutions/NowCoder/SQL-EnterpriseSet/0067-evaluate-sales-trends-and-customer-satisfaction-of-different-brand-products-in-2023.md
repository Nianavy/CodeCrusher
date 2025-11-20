https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL108`

```sql
SELECT 
    a.brand_id,
    ROUND(SUM(a.sales_amount), 2) AS total_sales_amount,  
    SUM(a.sales_quantity) AS total_sales_quantity,        
    ROUND(AVG(b.satisfaction_score), 2) AS avg_satisfaction_score  
FROM 
    sales_data AS a
JOIN 
    customer_feedback AS b ON a.sales_id = b.sales_id
WHERE 
    a.sales_month between '2023-01' and '2023-12'
GROUP BY 
    a.brand_id
ORDER BY 
    a.brand_id ASC;
```
